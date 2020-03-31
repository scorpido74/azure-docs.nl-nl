---
title: Azure Service Fabric CLI- sfctl-service
description: Meer informatie over sfctl, de opdrachtregelinterface van Azure Service Fabric. Bevat een lijst met opdrachten voor het beheren van services, servicetypen en servicepakketten.
author: jeffj6123
ms.topic: reference
ms.date: 1/16/2020
ms.author: jejarry
ms.openlocfilehash: 696de713129ca71dd7f2451501a7cc9eca0ee9b9
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "76906237"
---
# <a name="sfctl-service"></a>sfctl service
Service-, servicetypen en servicepakketten maken, verwijderen en beheren.

## <a name="commands"></a>Opdrachten

|Opdracht|Beschrijving|
| --- | --- |
| app-naam | Krijgt de naam van de Service Fabric-toepassing voor een service. |
| code-pakket-lijst | Hier wordt de lijst met codepakketten geïmplementeerd op een Service Fabric-knooppunt. |
| maken | Hiermee maakt u de opgegeven Service Fabric-service. |
| delete | Hiermee verwijdert u een bestaande Service Fabric-service. |
| geïmplementeerd-type | Hier wordt de informatie over een opgegeven servicetype van de toepassing geïmplementeerd op een knooppunt in een cluster servicestructuur. |
| geïmplementeerde typelijst | Hier wordt de lijst met de informatie over servicetypen opgehaald uit de toepassingen die zijn geïmplementeerd op een knooppunt in een cluster van servicefabric. |
| description | Krijgt de beschrijving van een bestaande Service Fabric-service. |
| get-container-logs | Hier worden de containerlogboeken voor container geïmplementeerd op een Service Fabric-knooppunt. |
| Gezondheid | Krijgt de status van de opgegeven Service Fabric-service. |
| Info | Krijgt de informatie over de specifieke service die behoort tot de Service Fabric-toepassing. |
| list | Hier vindt u informatie over alle services die behoren tot de toepassing die is opgegeven door de toepassings-id. |
| manifest | Hier wordt het manifest opgetje met een servicetype. |
| pakketimplementeren | Downloadt pakketten die zijn gekoppeld aan een opgegeven servicemanifest naar de afbeeldingscache op een opgegeven knooppunt. |
| pakket-gezondheid | Hier wordt de informatie over de status van een servicepakket voor een specifieke toepassing geïmplementeerd voor een Service Fabric-knooppunt en -toepassing. |
| pakket-info | Hier wordt de lijst met servicepakketten weergegeven die zijn geïmplementeerd op een Service Fabric-knooppunt dat exact overeenkomt met de opgegeven naam. |
| pakketlijst | Hier wordt de lijst met servicepakketten geïmplementeerd op een Service Fabric-knooppunt. |
| herstellen | Geeft aan het cluster Servicefabric aan dat het moet proberen de opgegeven service te herstellen die momenteel vastzit in quorumverlies. |
| rapport-gezondheid | Hiermee stuurt u een gezondheidsrapport over de Service Fabric-service. |
| Oplossen | Een servicefabricpartitie oplossen. |
| typelijst | Hier wordt de lijst weergegeven met de informatie over servicetypen die worden ondersteund door een ingerichte toepassingstype in een cluster servicestructuur. |
| update | Werkt de opgegeven service bij met behulp van de opgegeven updatebeschrijving. |

## <a name="sfctl-service-app-name"></a>sfctl service app-naam
Krijgt de naam van de Service Fabric-toepassing voor een service.

Hier krijgt u de naam van de toepassing voor de opgegeven service. Een fout van 404 FABRIC_E_SERVICE_DOES_NOT_EXIST wordt geretourneerd als er geen service met de meegeleverde service-id bestaat.

### <a name="arguments"></a>Argumenten

|Argument|Beschrijving|
| --- | --- |
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

## <a name="sfctl-service-code-package-list"></a>sfctl service code-package-list
Hier wordt de lijst met codepakketten geïmplementeerd op een Service Fabric-knooppunt.

Hier wordt de lijst met codepakketten geïmplementeerd op een Service Fabric-knooppunt voor de opgegeven toepassing.

### <a name="arguments"></a>Argumenten

|Argument|Beschrijving|
| --- | --- |
| --application-id [Vereist] | De identiteit van de applicatie. Dit is meestal de volledige naam van\:de toepassing zonder de 'stof ' URI regeling. Vanaf versie 6.0 worden hiërarchische namen\~afgebakend met het " teken" Als de toepassingsnaam bijvoorbeeld "fabric\:/myapp/app1" is, is\~de identiteit van de toepassing "myapp app1" in 6.0+ en "myapp/app1" in eerdere versies. |
| --node-name [Vereist] | De naam van het knooppunt. |
| --code-pakket-naam | De naam van het codepakket dat is opgegeven in het servicemanifest dat is geregistreerd als onderdeel van een toepassingstype in een cluster servicestructuur. |
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

## <a name="sfctl-service-create"></a>sfctl-service maken
Hiermee maakt u de opgegeven Service Fabric-service.

### <a name="arguments"></a>Argumenten

|Argument|Beschrijving|
| --- | --- |
| --app-id [Vereist] | De identiteit van de applicatie. Dit is meestal de volledige naam van\:de toepassing zonder de 'stof ' URI regeling. Vanaf versie 6.0 worden hiërarchische namen\~afgebakend met het ' teken.' Als de toepassingsnaam bijvoorbeeld 'fabric\:/myapp/app1' is, is de\~toepassingsidentiteit 'myapp app1' in 6.0+ en 'myapp/app1' in eerdere versies. |
| --naam [Vereist] | Naam van de dienst. Dit moet een kind van de applicatie-ID zijn. Dit is de volledige `fabric\:` naam inclusief de URI. Bijvoorbeeld service `fabric\:/A/B` is een `fabric\:/A`kind van toepassing . |
| --service-type [Vereist] | Naam van het servicetype. |
| --activeringsmodus | De activeringsmodus voor het servicepakket. |
| --beperkingen | De plaatsingsbeperkingen als tekenreeks. Plaatsingsbeperkingen zijn booleaanse expressies op knooppunteigenschappen en maken het mogelijk om een service te beperken tot bepaalde knooppunten op basis van de servicevereisten. Als u bijvoorbeeld een service wilt plaatsen op knooppunten\:waar NodeType blauw is, geeft u de volgende vermelding "NodeColor == blauw". |
| --gecorreleerd-service | Naam van de doelservice om mee te correleren. |
| --correlatie | Correleer de service met een bestaande service met behulp van een uitlijningsaffiniteit. |
| --dns-naam | De DNS-naam van de te maken service. De DNS-systeemservice Service Fabric moet voor deze instelling zijn ingeschakeld. |
| aantal --instance- | Het aantal gevallen. Dit geldt alleen voor staatloze services. |
| --int-regeling | Geeft aan dat de service gelijkmatig moet worden verdeeld over een reeks niet-ondertekende gehele getallen. |
| --int-scheme-count | Het aantal partities binnen het gehele sleutelbereik dat moet worden gemaakt, als u een uniform geheel getalpartitieschema gebruikt. |
| --int-scheme-high | Het einde van het gehele getallenbereik van de sleutel, als u een uniform geheel getalpartitieschema gebruikt. |
| --int-scheme-low | Het begin van het gehele getallenbereik van de sleutel, als u een uniform geheel getalpartitieschema gebruikt. |
| --load-metrics | Json gecodeerde lijst van statistieken die worden gebruikt bij load balancing services tussen knooppunten. |
| --min-replica-set-size | De minimale replica set grootte als een getal. Dit geldt alleen voor stateful services. |
| --verhuiskosten | Hiermee geeft u de verhuiskosten voor de service op. Mogelijke waarden\: zijn 'Zero', 'Low', 'Medium', 'High', 'VeryHigh'. |
| --named-scheme | Geeft aan dat de service meerdere benoemde partities moet hebben. |
| --named-scheme-list | Json gecodeerde lijst van namen om de service te partitioneren, als het gebruik van de genoemde partitie regeling. |
| --niet-volgehouden staat | Als dit waar is, geeft dit aan dat de service geen permanente status heeft die is opgeslagen op de lokale schijf of alleen de status in het geheugen wordt opgeslagen. |
| --plaatsing-beleidslijst | JSON codeerde lijst met plaatsingsbeleid voor de service en eventuele bijbehorende domeinnamen. Beleid kan een of\: `NonPartiallyPlaceService` `PreferPrimaryDomain`meer `RequireDomain` `RequireDomainDistribution`van , , , . |
| --quorum-verlies-wacht | De maximale duur, in seconden, waarvoor een partitie zich in een staat van quorumverlies mag begeven. Dit geldt alleen voor stateful services. |
| --replica-herstart-wachten | De duur, in seconden, tussen het moment waarop een replica naar beneden gaat en wanneer een nieuwe replica wordt gemaakt. Dit geldt alleen voor stateful services. |
| --scaling-policies | JSON codeerde lijst met schaalbeleid voor deze service. |
| --service-plaatsingstijd | De duur waarvoor replica's InBuild kunnen blijven voordat wordt gemeld dat build vastzit. Dit geldt alleen voor stateful services. |
| --singleton-regeling | Geeft aan dat de service één partitie moet hebben of een niet-partitieservice moet zijn. |
| --stand-by-replica-keep | De maximale duur, in seconden, waarvoor stand-by replica's worden gehandhaafd voordat ze worden verwijderd. Dit geldt alleen voor stateful services. |
| --stateful | Geeft aan dat de service een stateful service is. |
| --staatloos | Geeft aan dat de service een statusloze service is. |
| --doel-replica-set-size | De doelreplica stelt de grootte in als een getal. Dit geldt alleen voor stateful services. |
| --time-out -t | Standaard\: 60. |

### <a name="global-arguments"></a>Globale argumenten

|Argument|Beschrijving|
| --- | --- |
| --foutopsporing | Verhoog de logboekregistratie om alle foutopsporingslogboeken weer te geven. |
| --help -h | Dit helpbericht weergeven en afsluiten. |
| --output -o | Uitvoerindeling.  Toegestane waarden\: json, jsonc, tabel, tsv.  Standaard\: json. |
| --query | JMESPath-querytekenreeks. Zie\:http -jmespath.org/ voor meer informatie en voorbeelden. |
| --verbose | Verhoog de houtkap. Gebruik --debug voor volledige foutopsporingslogboeken. |

## <a name="sfctl-service-delete"></a>sfctl-service verwijderen
Hiermee verwijdert u een bestaande Service Fabric-service.

Er moet een service worden gemaakt voordat deze kan worden verwijderd. Standaard probeert Service Fabric servicereplica's op een sierlijke manier te sluiten en de service vervolgens te verwijderen. Als de service echter problemen heeft met het op een elegante manier sluiten van de replica, kan de verwijderingsbewerking lang duren of vast komen te zitten. Gebruik de optionele ForceRemove-vlag om de sierlijke close sequence over te slaan en de service krachtig te verwijderen.

### <a name="arguments"></a>Argumenten

|Argument|Beschrijving|
| --- | --- |
| --service-id [Vereist] | De identiteit van de dienst. Deze ID is meestal de volledige naam van\:de dienst zonder de 'stof ' URI regeling. Vanaf versie 6.0 worden hiërarchische namen\~afgebakend met het " teken" Als de servicenaam bijvoorbeeld "fabric\:/myapp/app1/svc1" is, is\~de\~service-identiteit "myapp app1 svc1" in 6.0+ en "myapp/app1/svc1" in eerdere versies. |
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

## <a name="sfctl-service-deployed-type"></a>sfctl-service geïmplementeerd
Hier wordt de informatie over een opgegeven servicetype van de toepassing geïmplementeerd op een knooppunt in een cluster servicestructuur.

Hier wordt de lijst met de informatie over een specifiek servicetype opgehaald uit de toepassingen die zijn geïmplementeerd op een knooppunt in een cluster van servicefabric. Het antwoord bevat de naam van het servicetype, de registratiestatus, het codepakket dat het heeft geregistreerd en activerings-id van het servicepakket. Elke vermelding vertegenwoordigt één activering van een servicetype, onderscheiden door de activerings-ID.

### <a name="arguments"></a>Argumenten

|Argument|Beschrijving|
| --- | --- |
| --application-id [Vereist] | De identiteit van de applicatie. Dit is meestal de volledige naam van\:de toepassing zonder de 'stof ' URI regeling. Vanaf versie 6.0 worden hiërarchische namen\~afgebakend met het " teken" Als de toepassingsnaam bijvoorbeeld "fabric\:/myapp/app1" is, is\~de identiteit van de toepassing "myapp app1" in 6.0+ en "myapp/app1" in eerdere versies. |
| --node-name [Vereist] | De naam van het knooppunt. |
| --service-type-naam [Vereist] | Hiermee geeft u de naam op van een servicetype Service Fabric. |
| --service-manifest-naam | De naam van het servicemanifest om de lijst met geïmplementeerde servicetypegegevens te filteren. Indien opgegeven, bevat het antwoord alleen de informatie over servicetypen die zijn gedefinieerd in dit servicemanifest. |
| --time-out -t | De time-out van de server voor het uitvoeren van de bewerking in enkele seconden. Deze time-out geeft de tijdsduur aan die de client bereid is te wachten tot de gevraagde bewerking is voltooid. De standaardwaarde voor deze parameter is 60 seconden.  Standaard\: 60. |

### <a name="global-arguments"></a>Globale argumenten

|Argument|Beschrijving|
| --- | --- |
| --foutopsporing | Verhoog de logboekregistratie om alle foutopsporingslogboeken weer te geven. |
| --help -h | Dit helpbericht weergeven en afsluiten. |
| --output -o | Uitvoerindeling.  Toegestane waarden\: json, jsonc, tabel, tsv.  Standaard\: json. |
| --query | JMESPath-querytekenreeks. Zie\:http -jmespath.org/ voor meer informatie en voorbeelden. |
| --verbose | Verhoog de houtkap. Gebruik --debug voor volledige foutopsporingslogboeken. |

## <a name="sfctl-service-deployed-type-list"></a>sfctl-service geïmplementeerd-type-lijst
Hier wordt de lijst met de informatie over servicetypen opgehaald uit de toepassingen die zijn geïmplementeerd op een knooppunt in een cluster van servicefabric.

Hier wordt de lijst met de informatie over servicetypen opgehaald uit de toepassingen die zijn geïmplementeerd op een knooppunt in een cluster van servicefabric. Het antwoord bevat de naam van het servicetype, de registratiestatus, het codepakket dat het heeft geregistreerd en activerings-id van het servicepakket.

### <a name="arguments"></a>Argumenten

|Argument|Beschrijving|
| --- | --- |
| --application-id [Vereist] | De identiteit van de applicatie. Dit is meestal de volledige naam van\:de toepassing zonder de 'stof ' URI regeling. Vanaf versie 6.0 worden hiërarchische namen\~afgebakend met het " teken" Als de toepassingsnaam bijvoorbeeld "fabric\:/myapp/app1" is, is\~de identiteit van de toepassing "myapp app1" in 6.0+ en "myapp/app1" in eerdere versies. |
| --node-name [Vereist] | De naam van het knooppunt. |
| --service-manifest-naam | De naam van het servicemanifest om de lijst met geïmplementeerde servicetypegegevens te filteren. Indien opgegeven, bevat het antwoord alleen de informatie over servicetypen die zijn gedefinieerd in dit servicemanifest. |
| --time-out -t | De time-out van de server voor het uitvoeren van de bewerking in enkele seconden. Deze time-out geeft de tijdsduur aan die de client bereid is te wachten tot de gevraagde bewerking is voltooid. De standaardwaarde voor deze parameter is 60 seconden.  Standaard\: 60. |

### <a name="global-arguments"></a>Globale argumenten

|Argument|Beschrijving|
| --- | --- |
| --foutopsporing | Verhoog de logboekregistratie om alle foutopsporingslogboeken weer te geven. |
| --help -h | Dit helpbericht weergeven en afsluiten. |
| --output -o | Uitvoerindeling.  Toegestane waarden\: json, jsonc, tabel, tsv.  Standaard\: json. |
| --query | JMESPath-querytekenreeks. Zie\:http -jmespath.org/ voor meer informatie en voorbeelden. |
| --verbose | Verhoog de houtkap. Gebruik --debug voor volledige foutopsporingslogboeken. |

## <a name="sfctl-service-description"></a>sfctl-servicebeschrijving
Krijgt de beschrijving van een bestaande Service Fabric-service.

Krijgt de beschrijving van een bestaande Service Fabric-service. Een service moet worden gemaakt voordat de beschrijving kan worden verkregen.

### <a name="arguments"></a>Argumenten

|Argument|Beschrijving|
| --- | --- |
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

## <a name="sfctl-service-get-container-logs"></a>sfctl service get-container-logs
Hier worden de containerlogboeken voor container geïmplementeerd op een Service Fabric-knooppunt.

Hier worden de containerlogboeken voor container geïmplementeerd op een Service Fabric-knooppunt voor het opgegeven codepakket.

### <a name="arguments"></a>Argumenten

|Argument|Beschrijving|
| --- | --- |
| --application-id [Vereist] | De identiteit van de applicatie. Dit is meestal de volledige naam van\:de toepassing zonder de 'stof ' URI regeling. Vanaf versie 6.0 worden hiërarchische namen\~afgebakend met het " teken" Als de toepassingsnaam bijvoorbeeld "fabric\:/myapp/app1" is, is\~de identiteit van de toepassing "myapp app1" in 6.0+ en "myapp/app1" in eerdere versies. |
| --code-package-name [Vereist] | De naam van het codepakket dat is opgegeven in het servicemanifest dat is geregistreerd als onderdeel van een toepassingstype in een cluster servicestructuur. |
| --node-name [Vereist] | De naam van het knooppunt. |
| --service-manifest-naam [Vereist] | De naam van een servicemanifest dat is geregistreerd als onderdeel van een toepassingstype in een cluster servicestructuur. |
| --vorige | Hiermee geeft u op of containerlogboeken moeten worden opgehaald uit afgesloten/dode containers van de instantie codepakket. |
| --staart | Aantal regels dat vanaf het einde van de logboeken moet worden weergegeven. Standaard is 100. 'alles' om de volledige logs weer te geven. |
| --time-out -t | De time-out van de server voor het uitvoeren van de bewerking in enkele seconden. Deze time-out geeft de tijdsduur aan die de client bereid is te wachten tot de gevraagde bewerking is voltooid. De standaardwaarde voor deze parameter is 60 seconden.  Standaard\: 60. |

### <a name="global-arguments"></a>Globale argumenten

|Argument|Beschrijving|
| --- | --- |
| --foutopsporing | Verhoog de logboekregistratie om alle foutopsporingslogboeken weer te geven. |
| --help -h | Dit helpbericht weergeven en afsluiten. |
| --output -o | Uitvoerindeling.  Toegestane waarden\: json, jsonc, tabel, tsv.  Standaard\: json. |
| --query | JMESPath-querytekenreeks. Zie\:http -jmespath.org/ voor meer informatie en voorbeelden. |
| --verbose | Verhoog de houtkap. Gebruik --debug voor volledige foutopsporingslogboeken. |

## <a name="sfctl-service-health"></a>sfctl service gezondheid
Krijgt de status van de opgegeven Service Fabric-service.

Krijgt de gezondheidsinformatie van de opgegeven service. Gebruik EventsHealthStateFilter om het verzamelen van statusgebeurtenissen die op de service zijn gerapporteerd op basis van de status te filteren. Gebruik PartitionsHealthStateFilter om de verzameling geretourneerde partities te filteren. Als u een service opgeeft die niet in het gezondheidsarchief bestaat, wordt in deze aanvraag een fout geretourneerd.

### <a name="arguments"></a>Argumenten

|Argument|Beschrijving|
| --- | --- |
| --service-id [Vereist] | De identiteit van de dienst. Deze ID is meestal de volledige naam van\:de dienst zonder de 'stof ' URI regeling. Vanaf versie 6.0 worden hiërarchische namen\~afgebakend met het " teken" Als de servicenaam bijvoorbeeld "fabric\:/myapp/app1/svc1" is, is\~de\~service-identiteit "myapp app1 svc1" in 6.0+ en "myapp/app1/svc1" in eerdere versies. |
| --events-health-state-filter | Hiermee u de verzameling HealthEvent-objecten filteren die zijn geretourneerd op basis van de status. De mogelijke waarden voor deze parameter omvatten de gehele waarde van een van de volgende statussen. Alleen gebeurtenissen die overeenkomen met het filter worden geretourneerd. Alle gebeurtenissen worden gebruikt om de geaggregeerde status te evalueren. Als dit niet is opgegeven, worden alle items geretourneerd. De statuswaarden zijn op vlag gebaseerde opsomming, dus de waarde kan een combinatie zijn van deze waarden, verkregen met behulp van de bitwise 'OR'-operator. Als de opgegeven waarde bijvoorbeeld 6 is, worden alle gebeurtenissen met de statuswaarde Van OK (2) en Waarschuwing (4) geretourneerd.  <br> - Standaard - Standaardwaarde. Komt overeen met elke HealthState. De waarde is nul.  <br> - Geen - Filter dat niet overeenkomt met een HealthState-waarde. Gebruikt om geen resultaten op een bepaalde verzameling van staten terug te keren. De waarde is 1.  <br> - Ok - Filter dat overeenkomt met invoer met HealthState-waarde Ok. De waarde is 2.  <br> - Waarschuwing - Filter dat overeenkomt met invoer met Waarschuwing voor de waarde HealthState. De waarde is 4.  <br> - Fout - Filter dat overeenkomt met invoer met Statuswaardefout. De waarde is 8.  <br> - Alles - Filter dat overeenkomt met de invoer met een healthstate-waarde. De waarde is 65535. |
| --exclude-health-statistics | Geeft aan of de statusstatistieken moeten worden geretourneerd als onderdeel van het queryresultaat. Standaard onwaar. De statistieken tonen het aantal kinderen entiteiten in de gezondheidstoestand Ok, Waarschuwing en Fout. |
| --partities-health-state-filter | Hiermee u het filteren van de statusobjecten van de partities die zijn geretourneerd in het resultaat van de servicestatusquery op basis van hun status. De mogelijke waarden voor deze parameter omvatten de gehele waarde van een van de volgende statussen. Alleen partities die overeenkomen met het filter worden geretourneerd. Alle partities worden gebruikt om de geaggregeerde status te evalueren. Als dit niet is opgegeven, worden alle items geretourneerd. De statuswaarden zijn op vlag gebaseerde opsomming, dus de waarde kan een combinatie zijn van deze waarde die wordt verkregen met bitwise 'OR'-operator. Als de opgegeven waarde bijvoorbeeld 6 is, wordt de status van partities met healthstate-waarde van OK (2) en Waarschuwing (4) geretourneerd.  <br> - Standaard - Standaardwaarde. Komt overeen met elke HealthState. De waarde is nul.  <br> - Geen - Filter dat niet overeenkomt met een HealthState-waarde. Gebruikt om geen resultaten op een bepaalde verzameling van staten terug te keren. De waarde is 1.  <br> - Ok - Filter dat overeenkomt met invoer met HealthState-waarde Ok. De waarde is 2.  <br> - Waarschuwing - Filter dat overeenkomt met invoer met Waarschuwing voor de waarde HealthState. De waarde is 4.  <br> - Fout - Filter dat overeenkomt met invoer met Statuswaardefout. De waarde is 8.  <br> - Alles - Filter dat overeenkomt met de invoer met een healthstate-waarde. De waarde is 65535. |
| --time-out -t | De time-out van de server voor het uitvoeren van de bewerking in enkele seconden. Deze time-out geeft de tijdsduur aan die de client bereid is te wachten tot de gevraagde bewerking is voltooid. De standaardwaarde voor deze parameter is 60 seconden.  Standaard\: 60. |

### <a name="global-arguments"></a>Globale argumenten

|Argument|Beschrijving|
| --- | --- |
| --foutopsporing | Verhoog de logboekregistratie om alle foutopsporingslogboeken weer te geven. |
| --help -h | Dit helpbericht weergeven en afsluiten. |
| --output -o | Uitvoerindeling.  Toegestane waarden\: json, jsonc, tabel, tsv.  Standaard\: json. |
| --query | JMESPath-querytekenreeks. Zie\:http -jmespath.org/ voor meer informatie en voorbeelden. |
| --verbose | Verhoog de houtkap. Gebruik --debug voor volledige foutopsporingslogboeken. |

## <a name="sfctl-service-info"></a>sfctl service info
Krijgt de informatie over de specifieke service die behoort tot de Service Fabric-toepassing.

Retourneert de informatie over de opgegeven service die behoort tot de opgegeven Service Fabric-toepassing.

### <a name="arguments"></a>Argumenten

|Argument|Beschrijving|
| --- | --- |
| --application-id [Vereist] | De identiteit van de applicatie. Dit is meestal de volledige naam van\:de toepassing zonder de 'stof ' URI regeling. Vanaf versie 6.0 worden hiërarchische namen\~afgebakend met het " teken" Als de toepassingsnaam bijvoorbeeld "fabric\:/myapp/app1" is, is\~de identiteit van de toepassing "myapp app1" in 6.0+ en "myapp/app1" in eerdere versies. |
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

## <a name="sfctl-service-list"></a>sfctl-servicelijst
Hier vindt u informatie over alle services die behoren tot de toepassing die is opgegeven door de toepassings-id.

Retourneert de informatie over alle services die behoren tot de toepassing die is opgegeven door de toepassings-id.

### <a name="arguments"></a>Argumenten

|Argument|Beschrijving|
| --- | --- |
| --application-id [Vereist] | De identiteit van de applicatie. Dit is meestal de volledige naam van\:de toepassing zonder de 'stof ' URI regeling. Vanaf versie 6.0 worden hiërarchische namen\~afgebakend met het " teken" Als de toepassingsnaam bijvoorbeeld "fabric\:/myapp/app1" is, is\~de identiteit van de toepassing "myapp app1" in 6.0+ en "myapp/app1" in eerdere versies. |
| --continuation-token | De parameter vervolgtoken wordt gebruikt om volgende set resultaten te verkrijgen. Een vervolgtoken met een niet-lege waarde wordt opgenomen in de reactie van de API wanneer de resultaten van het systeem niet in één reactie passen. Wanneer deze waarde wordt doorgegeven aan de volgende API-aanroep, retourneert de API de volgende set resultaten. Als er geen verdere resultaten zijn, bevat het vervolgtoken geen waarde. De waarde van deze parameter mag niet worden gecodeerd met url's. |
| --service-type-naam | De servicetypenaam die wordt gebruikt om de services te filteren waarvoor u wilt zoeken. |
| --time-out -t | De time-out van de server voor het uitvoeren van de bewerking in enkele seconden. Deze time-out geeft de tijdsduur aan die de client bereid is te wachten tot de gevraagde bewerking is voltooid. De standaardwaarde voor deze parameter is 60 seconden.  Standaard\: 60. |

### <a name="global-arguments"></a>Globale argumenten

|Argument|Beschrijving|
| --- | --- |
| --foutopsporing | Verhoog de logboekregistratie om alle foutopsporingslogboeken weer te geven. |
| --help -h | Dit helpbericht weergeven en afsluiten. |
| --output -o | Uitvoerindeling.  Toegestane waarden\: json, jsonc, tabel, tsv.  Standaard\: json. |
| --query | JMESPath-querytekenreeks. Zie\:http -jmespath.org/ voor meer informatie en voorbeelden. |
| --verbose | Verhoog de houtkap. Gebruik --debug voor volledige foutopsporingslogboeken. |

## <a name="sfctl-service-manifest"></a>sfctl servicemanifest
Hier wordt het manifest opgetje met een servicetype.

Hier wordt het manifest opgetje met een servicetype. Het antwoord bevat het servicemanifest XML als tekenreeks.

### <a name="arguments"></a>Argumenten

|Argument|Beschrijving|
| --- | --- |
| --application-type-name [Vereist] | De naam van het toepassingstype. |
| --application-type-versie [Vereist] | De versie van het toepassingstype. |
| --service-manifest-naam [Vereist] | De naam van een servicemanifest dat is geregistreerd als onderdeel van een toepassingstype in een cluster servicestructuur. |
| --time-out -t | De time-out van de server voor het uitvoeren van de bewerking in enkele seconden. Deze time-out geeft de tijdsduur aan die de client bereid is te wachten tot de gevraagde bewerking is voltooid. De standaardwaarde voor deze parameter is 60 seconden.  Standaard\: 60. |

### <a name="global-arguments"></a>Globale argumenten

|Argument|Beschrijving|
| --- | --- |
| --foutopsporing | Verhoog de logboekregistratie om alle foutopsporingslogboeken weer te geven. |
| --help -h | Dit helpbericht weergeven en afsluiten. |
| --output -o | Uitvoerindeling.  Toegestane waarden\: json, jsonc, tabel, tsv.  Standaard\: json. |
| --query | JMESPath-querytekenreeks. Zie\:http -jmespath.org/ voor meer informatie en voorbeelden. |
| --verbose | Verhoog de houtkap. Gebruik --debug voor volledige foutopsporingslogboeken. |

## <a name="sfctl-service-package-deploy"></a>sfctl service pakket-deploy
Downloadt pakketten die zijn gekoppeld aan een opgegeven servicemanifest naar de afbeeldingscache op een opgegeven knooppunt.

### <a name="arguments"></a>Argumenten

|Argument|Beschrijving|
| --- | --- |
| --app-type-naam [Vereist] | De naam van het aanvraagmanifest voor het overeenkomstige gevraagde servicemanifest. |
| --app-type-versie [Vereist] | De versie van het toepassingsmanifest voor het bijbehorende gevraagde servicemanifest. |
| --node-name [Vereist] | De naam van het knooppunt. |
| --service-manifest-naam [Vereist] | De naam van de service manifest in verband met de pakketten die zullen worden gedownload. |
| --share-beleid | JSON gecodeerde lijst van het delen van beleid. Elk element van het deelbeleid bestaat uit een 'naam' en 'scope'. De naam komt overeen met de naam van de code, configuratie of gegevenspakket die moet worden gedeeld. Het bereik kan 'Geen', 'Alles', 'Code', 'Config' of 'Data' zijn. |
| --time-out -t | Standaard\: 60. |

### <a name="global-arguments"></a>Globale argumenten

|Argument|Beschrijving|
| --- | --- |
| --foutopsporing | Verhoog de logboekregistratie om alle foutopsporingslogboeken weer te geven. |
| --help -h | Dit helpbericht weergeven en afsluiten. |
| --output -o | Uitvoerindeling.  Toegestane waarden\: json, jsonc, tabel, tsv.  Standaard\: json. |
| --query | JMESPath-querytekenreeks. Zie\:http -jmespath.org/ voor meer informatie en voorbeelden. |
| --verbose | Verhoog de houtkap. Gebruik --debug voor volledige foutopsporingslogboeken. |

## <a name="sfctl-service-package-health"></a>sfctl service pakket-health
Hier wordt de informatie over de status van een servicepakket voor een specifieke toepassing geïmplementeerd voor een Service Fabric-knooppunt en -toepassing.

Hier krijgt u de informatie over de status van een servicepakket voor een specifieke toepassing die is geïmplementeerd op een servicefabricknooppunt. Gebruik EventsHealthStateFilter om optioneel te filteren op de verzameling HealthEvent-objecten die worden gerapporteerd op het geïmplementeerde servicepakket op basis van de status.

### <a name="arguments"></a>Argumenten

|Argument|Beschrijving|
| --- | --- |
| --application-id [Vereist] | De identiteit van de applicatie. Dit is meestal de volledige naam van\:de toepassing zonder de 'stof ' URI regeling. Vanaf versie 6.0 worden hiërarchische namen\~afgebakend met het " teken" Als de toepassingsnaam bijvoorbeeld "fabric\:/myapp/app1" is, is\~de identiteit van de toepassing "myapp app1" in 6.0+ en "myapp/app1" in eerdere versies. |
| --node-name [Vereist] | De naam van het knooppunt. |
| --service-pakket-naam [Vereist] | De naam van het servicepakket. |
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

## <a name="sfctl-service-package-info"></a>sfctl service pakket-info
Hier wordt de lijst met servicepakketten weergegeven die zijn geïmplementeerd op een Service Fabric-knooppunt dat exact overeenkomt met de opgegeven naam.

Retourneert de informatie over de servicepakketten die zijn geïmplementeerd op een servicefabricknooppunt voor de opgegeven toepassing. Deze resultaten zijn van servicepakketten waarvan de naam exact overeenkomt met de naam van het servicepakket dat als parameter is opgegeven.

### <a name="arguments"></a>Argumenten

|Argument|Beschrijving|
| --- | --- |
| --application-id [Vereist] | De identiteit van de applicatie. Dit is meestal de volledige naam van\:de toepassing zonder de 'stof ' URI regeling. Vanaf versie 6.0 worden hiërarchische namen\~afgebakend met het " teken" Als de toepassingsnaam bijvoorbeeld "fabric\:/myapp/app1" is, is\~de identiteit van de toepassing "myapp app1" in 6.0+ en "myapp/app1" in eerdere versies. |
| --node-name [Vereist] | De naam van het knooppunt. |
| --service-pakket-naam [Vereist] | De naam van het servicepakket. |
| --time-out -t | De time-out van de server voor het uitvoeren van de bewerking in enkele seconden. Deze time-out geeft de tijdsduur aan die de client bereid is te wachten tot de gevraagde bewerking is voltooid. De standaardwaarde voor deze parameter is 60 seconden.  Standaard\: 60. |

### <a name="global-arguments"></a>Globale argumenten

|Argument|Beschrijving|
| --- | --- |
| --foutopsporing | Verhoog de logboekregistratie om alle foutopsporingslogboeken weer te geven. |
| --help -h | Dit helpbericht weergeven en afsluiten. |
| --output -o | Uitvoerindeling.  Toegestane waarden\: json, jsonc, tabel, tsv.  Standaard\: json. |
| --query | JMESPath-querytekenreeks. Zie\:http -jmespath.org/ voor meer informatie en voorbeelden. |
| --verbose | Verhoog de houtkap. Gebruik --debug voor volledige foutopsporingslogboeken. |

## <a name="sfctl-service-package-list"></a>sfctl service pakket-lijst
Hier wordt de lijst met servicepakketten geïmplementeerd op een Service Fabric-knooppunt.

Retourneert de informatie over de servicepakketten die zijn geïmplementeerd op een servicefabricknooppunt voor de opgegeven toepassing.

### <a name="arguments"></a>Argumenten

|Argument|Beschrijving|
| --- | --- |
| --application-id [Vereist] | De identiteit van de applicatie. Dit is meestal de volledige naam van\:de toepassing zonder de 'stof ' URI regeling. Vanaf versie 6.0 worden hiërarchische namen\~afgebakend met het " teken" Als de toepassingsnaam bijvoorbeeld "fabric\:/myapp/app1" is, is\~de identiteit van de toepassing "myapp app1" in 6.0+ en "myapp/app1" in eerdere versies. |
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

## <a name="sfctl-service-recover"></a>sfctl service herstellen
Geeft aan het cluster Servicefabric aan dat het moet proberen de opgegeven service te herstellen die momenteel vastzit in quorumverlies.

Geeft aan het cluster Servicefabric aan dat het moet proberen de opgegeven service te herstellen die momenteel vastzit in quorumverlies. Deze bewerking mag alleen worden uitgevoerd als bekend is dat de replica's die zijn uitgeschakeld niet kunnen worden hersteld. Onjuist gebruik van deze API kan leiden tot mogelijk gegevensverlies.

### <a name="arguments"></a>Argumenten

|Argument|Beschrijving|
| --- | --- |
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

## <a name="sfctl-service-report-health"></a>sfctl service report-health
Hiermee stuurt u een gezondheidsrapport over de Service Fabric-service.

Rapporteert de status van de opgegeven Service Fabric-service. Het rapport moet de informatie bevatten over de bron van het gezondheidsrapport en de eigenschap waarop het wordt gerapporteerd. Het rapport wordt verzonden naar een Service Fabric-gatewayservice, die doorstuurt naar de gezondheidswinkel. Het rapport kan worden geaccepteerd door de gateway, maar afgewezen door het gezondheidsarchief na extra validatie. Het statusarchief kan het rapport bijvoorbeeld afwijzen vanwege een ongeldige parameter, zoals een verouderd volgnummer. Als u wilt zien of het rapport is toegepast in het gezondheidsarchief, controleert u of het rapport wordt weergegeven in de statusgebeurtenissen van de service.

### <a name="arguments"></a>Argumenten

|Argument|Beschrijving|
| --- | --- |
| --health-property [Vereist] | De eigenschap van de gezondheidsinformatie. <br><br> Een entiteit kan statusrapporten voor verschillende eigenschappen hebben. De eigenschap is een tekenreeks en geen vaste opsomming om de verslaggever flexibiliteit om de toestand voorwaarde die het rapport triggers categoriseren. Een verslaggever met SourceId "LocalWatchdog" kan bijvoorbeeld de status van de beschikbare schijf op een knooppunt controleren, zodat het de eigenschap 'Beschikbare schijf' op dat knooppunt kan rapporteren. Dezelfde verslaggever kan de verbinding met het knooppunt controleren, zodat hij een eigenschap "Connectiviteit" op hetzelfde knooppunt kan melden. In het gezondheidsarchief worden deze rapporten behandeld als afzonderlijke statusgebeurtenissen voor het opgegeven knooppunt. Samen met de SourceId identificeert het pand op unieke wijze de gezondheidsinformatie. |
| --gezondheidstoestand [Vereist] | Mogelijke waarden\: zijn 'Ongeldig', 'Ok', 'Waarschuwing', 'Fout', 'Onbekend'. |
| --service-id [Vereist] | De identiteit van de dienst. <br><br> Dit is meestal de volledige naam van\:de dienst zonder de 'stof ' URI regeling. Vanaf versie 6.0 worden hiërarchische namen\~afgebakend met het ' teken.' Als de servicenaam bijvoorbeeld 'fabric\:/myapp/app1/svc1' is, is de\~service-identiteit 'myapp app1\~svc1' in 6.0+ en 'myapp/app1/svc1' in eerdere versies. |
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

## <a name="sfctl-service-resolve"></a>sfctl-service is opgelost
Een servicefabricpartitie oplossen.

Een servicepartitie voor servicefabric oplossen om de eindpunten van de servicereplica's te krijgen.

### <a name="arguments"></a>Argumenten

|Argument|Beschrijving|
| --- | --- |
| --service-id [Vereist] | De identiteit van de dienst. Deze ID is meestal de volledige naam van\:de dienst zonder de 'stof ' URI regeling. Vanaf versie 6.0 worden hiërarchische namen\~afgebakend met het " teken" Als de servicenaam bijvoorbeeld "fabric\:/myapp/app1/svc1" is, is\~de\~service-identiteit "myapp app1 svc1" in 6.0+ en "myapp/app1/svc1" in eerdere versies. |
| --partitie-sleutel-type | Sleuteltype voor de partitie. Deze parameter is vereist als het partitieschema voor de service Int64Range of Named is. De mogelijke waarden volgen. - Geen (1) - Geeft aan dat de parameter PartitionKeyValue niet is opgegeven. Dit geldt voor de partities met partitieschema als Singleton. Dit is de standaardwaarde. De waarde is 1. - Int64Range (2) - Geeft aan dat de parameter PartitionKeyValue een int64-partitiesleutel is. Dit geldt voor de partities met partitieschema als Int64Range. De waarde is 2. - Benoemd (3) - Geeft aan dat de parameter PartitionKeyValue een naam van de partitie is. Dit geldt voor de partities met partitieschema als Benoemd. De waarde is 3. |
| --partitiesleutelwaarde | Partitiesleutel. Dit is vereist als het partitieschema voor de service Int64Range of Named is. Dit is niet de partitie-id, maar eerder de gehele sleutelwaarde of de naam van de partitie-id. Als uw service bijvoorbeeld bereikpartities van 0 tot 10 gebruikt, is partitionkeyvalue een geheel getal in dat bereik. Queryservicebeschrijving om het bereik of de naam te bekijken. |
| --vorige rsp-versie | De waarde in het veld Versie van het antwoord dat eerder is ontvangen. Dit is vereist als de gebruiker weet dat het resultaat dat eerder is verkregen, verouderd is. |
| --time-out -t | De time-out van de server voor het uitvoeren van de bewerking in enkele seconden. Deze time-out geeft de tijdsduur aan die de client bereid is te wachten tot de gevraagde bewerking is voltooid. De standaardwaarde voor deze parameter is 60 seconden.  Standaard\: 60. |

### <a name="global-arguments"></a>Globale argumenten

|Argument|Beschrijving|
| --- | --- |
| --foutopsporing | Verhoog de logboekregistratie om alle foutopsporingslogboeken weer te geven. |
| --help -h | Dit helpbericht weergeven en afsluiten. |
| --output -o | Uitvoerindeling.  Toegestane waarden\: json, jsonc, tabel, tsv.  Standaard\: json. |
| --query | JMESPath-querytekenreeks. Zie\:http -jmespath.org/ voor meer informatie en voorbeelden. |
| --verbose | Verhoog de houtkap. Gebruik --debug voor volledige foutopsporingslogboeken. |

## <a name="sfctl-service-type-list"></a>sfctl-servicetypelijst
Hier wordt de lijst weergegeven met de informatie over servicetypen die worden ondersteund door een ingerichte toepassingstype in een cluster servicestructuur.

Hier wordt de lijst weergegeven met de informatie over servicetypen die worden ondersteund door een ingerichte toepassingstype in een cluster servicestructuur. Het opgegeven toepassingstype moet bestaan. Anders wordt een 404-status geretourneerd.

### <a name="arguments"></a>Argumenten

|Argument|Beschrijving|
| --- | --- |
| --application-type-name [Vereist] | De naam van het toepassingstype. |
| --application-type-versie [Vereist] | De versie van het toepassingstype. |
| --time-out -t | De time-out van de server voor het uitvoeren van de bewerking in enkele seconden. Deze time-out geeft de tijdsduur aan die de client bereid is te wachten tot de gevraagde bewerking is voltooid. De standaardwaarde voor deze parameter is 60 seconden.  Standaard\: 60. |

### <a name="global-arguments"></a>Globale argumenten

|Argument|Beschrijving|
| --- | --- |
| --foutopsporing | Verhoog de logboekregistratie om alle foutopsporingslogboeken weer te geven. |
| --help -h | Dit helpbericht weergeven en afsluiten. |
| --output -o | Uitvoerindeling.  Toegestane waarden\: json, jsonc, tabel, tsv.  Standaard\: json. |
| --query | JMESPath-querytekenreeks. Zie\:http -jmespath.org/ voor meer informatie en voorbeelden. |
| --verbose | Verhoog de houtkap. Gebruik --debug voor volledige foutopsporingslogboeken. |

## <a name="sfctl-service-update"></a>sfctl service-update
Werkt de opgegeven service bij met behulp van de opgegeven updatebeschrijving.

### <a name="arguments"></a>Argumenten

|Argument|Beschrijving|
| --- | --- |
| --service-id [Vereist] | De identiteit van de dienst. Dit is meestal de volledige naam van\:de dienst zonder de 'stof ' URI regeling. Vanaf versie 6.0 worden hiërarchische namen\~afgebakend met het " teken" Als de servicenaam bijvoorbeeld 'fabric\:/myapp/app1/svc1' is, is de\~service-identiteit 'myapp app1\~svc1' in 6.0+ en 'myapp/app1/svc1' in eerdere versies. |
| --beperkingen | De plaatsingsbeperkingen als tekenreeks. Plaatsingsbeperkingen zijn booleaanse expressies op knooppunteigenschappen en maken het mogelijk om een service te beperken tot bepaalde knooppunten op basis van de servicevereisten. Als u bijvoorbeeld een service wilt plaatsen op knooppunten\: waar NodeType blauw is, geeft u de volgende vermelding "NodeColor == blauw". |
| --gecorreleerd-service | Naam van de doelservice om mee te correleren. |
| --correlatie | Correleer de service met een bestaande service met behulp van een uitlijningsaffiniteit. |
| aantal --instance- | Het aantal gevallen. Dit geldt alleen voor staatloze services. |
| --load-metrics | Json gecodeerde lijst van statistieken die worden gebruikt bij het balanceren van de belasting tussen knooppunten. |
| --min-replica-set-size | De minimale replica set grootte als een getal. Dit geldt alleen voor stateful services. |
| --verhuiskosten | Hiermee geeft u de verhuiskosten voor de service op. Mogelijke waarden\: zijn 'Zero', 'Low', 'Medium', 'High', 'VeryHigh'. |
| --plaatsing-beleidslijst | JSON codeerde lijst met plaatsingsbeleid voor de service en eventuele bijbehorende domeinnamen. Beleid kan een of\: `NonPartiallyPlaceService` `PreferPrimaryDomain`meer `RequireDomain` `RequireDomainDistribution`van , , , . |
| --quorum-verlies-wacht | De maximale duur, in seconden, waarvoor een partitie zich in een staat van quorumverlies mag begeven. Dit geldt alleen voor stateful services. |
| --replica-herstart-wachten | De duur, in seconden, tussen het moment waarop een replica naar beneden gaat en wanneer een nieuwe replica wordt gemaakt. Dit geldt alleen voor stateful services. |
| --scaling-policies | JSON codeerde lijst met schaalbeleid voor deze service. |
| --service-plaatsingstijd | De duur waarvoor replica's InBuild kunnen blijven voordat wordt gemeld dat build vastzit. Dit geldt alleen voor stateful services. |
| --stand-by-replica-keep | De maximale duur, in seconden, waarvoor stand-by replica's worden gehandhaafd voordat ze worden verwijderd. Dit geldt alleen voor stateful services. |
| --stateful | Hiermee geeft u aan dat de doelservice een stateful service is. |
| --staatloos | Hiermee geeft u aan dat de doelservice een statusloze service is. |
| --doel-replica-set-size | De doelreplica stelt de grootte in als een getal. Dit geldt alleen voor stateful services. |
| --time-out -t | Standaard\: 60. |

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
