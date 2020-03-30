---
title: Azure Service Fabric CLI- sfctl-toepassing
description: Meer informatie over sfctl, de opdrachtregelinterface van Azure Service Fabric. Bevat een lijst met opdrachten voor het beheren van toepassingen.
author: jeffj6123
ms.topic: reference
ms.date: 1/16/2020
ms.author: jejarry
ms.openlocfilehash: b4e1066bba1db387c9dc0600bc55522f0b5fe897
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "76906208"
---
# <a name="sfctl-application"></a>sfctl application
Toepassingen en toepassingstypen maken, verwijderen en beheren.

## <a name="commands"></a>Opdrachten

|Opdracht|Beschrijving|
| --- | --- |
| maken | Hiermee maakt u een Service Fabric-toepassing met de opgegeven beschrijving. |
| delete | Hiermee verwijdert u een bestaande Service Fabric-toepassing. |
| Ingezet | Hier wordt de informatie over een toepassing geïmplementeerd op een servicefabricknooppunt. |
| ingezet-gezondheid | Hier wordt de informatie over de status van een toepassing die is geïmplementeerd op een servicefabricknooppunt. |
| geïmplementeerde lijst | Hier wordt de lijst met toepassingen geïmplementeerd op een Service Fabric-knooppunt. |
| Gezondheid | Krijgt de status van de service fabric applicatie. |
| Info | Hier vindt u informatie over een Service Fabric-toepassing. |
| list | Hiermee wordt de lijst met toepassingen weergegeven die zijn gemaakt in het cluster Servicefabric die overeenkomen met de opgegeven filters. |
| laden | Hier vindt u informatie over het laden over een Service Fabric-toepassing. |
| manifest | Hier wordt het manifest geplaatst waarin een toepassingstype wordt beschreven. |
| Bepaling | Voorziet of registreert een Service Fabric-toepassingstype met het cluster met behulp van het pakket '.sfpkg' in de externe winkel of met behulp van het toepassingspakket in de afbeeldingswinkel. |
| rapport-gezondheid | Hiermee verzendt u een gezondheidsrapport over de toepassing Service Fabric. |
| type | Hier wordt de lijst met toepassingstypen in het cluster Servicefabric weergegeven dat exact overeenkomt met de opgegeven naam. |
| typelijst | Hier wordt de lijst met toepassingstypen weergegeven in het cluster Servicefabric. |
| ontstellen | Hiermee verwijdert of verwijdert u een servicefabric-toepassingstype uit het cluster. |
| upgrade | Hiermee wordt gestart met het upgraden van een toepassing in het cluster Servicefabric. |
| upgrade-cv | Hervat het upgraden van een toepassing in het cluster ServiceFabric. |
| upgrade-rollback | Hiermee wordt de momenteel lopende upgrade van een toepassing in het cluster Service Fabric teruggedraaid. |
| upgradestatus | Krijgt details voor de nieuwste upgrade uitgevoerd op deze applicatie. |
| uploaden | Kopieer een servicefabric-toepassingspakket naar het afbeeldingsarchief. |

## <a name="sfctl-application-create"></a>sfctl-toepassing maken
Hiermee maakt u een Service Fabric-toepassing met de opgegeven beschrijving.

### <a name="arguments"></a>Argumenten

|Argument|Beschrijving|
| --- | --- |
| --app-naam [Vereist] | De naam van de aanvraag,\:met inbegrip van de 'stof ' URI-regeling. |
| --app-type [Vereist] | De naam van het toepassingstype in het toepassingsmanifest. |
| --app-versie [Vereist] | De versie van het toepassingstype zoals gedefinieerd in het toepassingsmanifest. |
| --max-node-count | Het maximum aantal knooppunten waarbij Service Fabric capaciteit voor deze toepassing reserveert. Dit betekent niet dat de services van deze toepassing op al deze knooppunten worden geplaatst. |
| --statistieken | Een JSON gecodeerde lijst van metrische beschrijvingen van toepassingscapaciteit. Een statistiek wordt gedefinieerd als een naam die is gekoppeld aan een set capaciteit voor elk knooppunt waarop de toepassing bestaat. |
| --min-node-count | Het minimumaantal knooppunten waarbij Service Fabric capaciteit voor deze toepassing zal reserveren. Dit betekent niet dat de services van deze toepassing op al deze knooppunten worden geplaatst. |
| --parameters | Een JSON gecodeerde lijst van toepassingsparameteroverschrijvingen die moeten worden toegepast bij het maken van de toepassing. |
| --time-out -t | Standaard\: 60. |

### <a name="global-arguments"></a>Globale argumenten

|Argument|Beschrijving|
| --- | --- |
| --foutopsporing | Verhoog de logboekregistratie om alle foutopsporingslogboeken weer te geven. |
| --help -h | Dit helpbericht weergeven en afsluiten. |
| --output -o | Uitvoerindeling.  Toegestane waarden\: json, jsonc, tabel, tsv.  Standaard\: json. |
| --query | JMESPath-querytekenreeks. Zie\:http -jmespath.org/ voor meer informatie en voorbeelden. |
| --verbose | Verhoog de houtkap. Gebruik --debug voor volledige foutopsporingslogboeken. |

## <a name="sfctl-application-delete"></a>sfctl-toepassing verwijderen
Hiermee verwijdert u een bestaande Service Fabric-toepassing.

Een toepassing moet worden gemaakt voordat deze kan worden verwijderd. Als u een toepassing verwijdert, worden alle services verwijderd die deel uitmaken van die toepassing. Standaard probeert Service Fabric servicereplica's op een sierlijke manier te sluiten en de service vervolgens te verwijderen. Als een service echter problemen heeft met het sluiten van de replica, kan de verwijderingsbewerking lang duren of vast komen te zitten. Gebruik de optionele ForceRemove-vlag om de sierlijke close sequence over te slaan en de toepassing en al zijn services krachtig te verwijderen.

### <a name="arguments"></a>Argumenten

|Argument|Beschrijving|
| --- | --- |
| --application-id [Vereist] | De identiteit van de applicatie. Dit is meestal de volledige naam van\:de toepassing zonder de 'stof ' URI regeling. Vanaf versie 6.0 worden hiërarchische namen\~afgebakend met het " teken" Als de toepassingsnaam bijvoorbeeld "fabric\:/myapp/app1" is, is\~de identiteit van de toepassing "myapp app1" in 6.0+ en "myapp/app1" in eerdere versies. |
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

## <a name="sfctl-application-deployed"></a>sfctl-toepassing geïmplementeerd
Hier wordt de informatie over een toepassing geïmplementeerd op een servicefabricknooppunt.

Met deze query worden systeemtoepassingsgegevens geretourneerd als de opgegeven toepassings-id voor de systeemtoepassing is. De resultaten omvatten geïmplementeerde toepassingen in actieve, activerende en downloadstatussen. Deze query vereist dat de naam van het knooppunt overeenkomt met een knooppunt in het cluster. De query mislukt als de opgegeven knooppuntnaam niet wijst op actieve Servicefabric-knooppunten in het cluster.

### <a name="arguments"></a>Argumenten

|Argument|Beschrijving|
| --- | --- |
| --application-id [Vereist] | De identiteit van de applicatie. Dit is meestal de volledige naam van\:de toepassing zonder de 'stof ' URI regeling. Vanaf versie 6.0 worden hiërarchische namen\~afgebakend met het " teken" Als de toepassingsnaam bijvoorbeeld "fabric\:/myapp/app1" is, is\~de identiteit van de toepassing "myapp app1" in 6.0+ en "myapp/app1" in eerdere versies. |
| --node-name [Vereist] | De naam van het knooppunt. |
| --include-health-state | De status van een entiteit opnemen. Als deze parameter onjuist is of niet is opgegeven, is de geretourneerde status 'Onbekend'. Wanneer deze is ingesteld op true, gaat de query parallel aan het knooppunt en de statusservice voordat de resultaten worden samengevoegd. Als gevolg hiervan is de query duurder en kan het langer duren. |
| --time-out -t | De time-out van de server voor het uitvoeren van de bewerking in enkele seconden. Deze time-out geeft de tijdsduur aan die de client bereid is te wachten tot de gevraagde bewerking is voltooid. De standaardwaarde voor deze parameter is 60 seconden.  Standaard\: 60. |

### <a name="global-arguments"></a>Globale argumenten

|Argument|Beschrijving|
| --- | --- |
| --foutopsporing | Verhoog de logboekregistratie om alle foutopsporingslogboeken weer te geven. |
| --help -h | Dit helpbericht weergeven en afsluiten. |
| --output -o | Uitvoerindeling.  Toegestane waarden\: json, jsonc, tabel, tsv.  Standaard\: json. |
| --query | JMESPath-querytekenreeks. Zie\:http -jmespath.org/ voor meer informatie en voorbeelden. |
| --verbose | Verhoog de houtkap. Gebruik --debug voor volledige foutopsporingslogboeken. |

## <a name="sfctl-application-deployed-health"></a>sfctl-toepassing geïmplementeerd-status
Hier wordt de informatie over de status van een toepassing die is geïmplementeerd op een servicefabricknooppunt.

Hier wordt de informatie over de status van een toepassing die is geïmplementeerd op een servicefabricknooppunt. Gebruik EventsHealthStateFilter om optioneel te filteren op de verzameling HealthEvent-objecten die zijn gerapporteerd over de geïmplementeerde toepassing op basis van de status. Gebruik DeployedServicePackagesHealthStateFilter om optioneel te filteren op geïmplementeerde ServicePackageHealth-kinderen op basis van de status.

### <a name="arguments"></a>Argumenten

|Argument|Beschrijving|
| --- | --- |
| --application-id [Vereist] | De identiteit van de applicatie. Dit is meestal de volledige naam van\:de toepassing zonder de 'stof ' URI regeling. Vanaf versie 6.0 worden hiërarchische namen\~afgebakend met het " teken" Als de toepassingsnaam bijvoorbeeld "fabric\:/myapp/app1" is, is\~de identiteit van de toepassing "myapp app1" in 6.0+ en "myapp/app1" in eerdere versies. |
| --node-name [Vereist] | De naam van het knooppunt. |
| --ployed-service-packages-health-state-filter | Hiermee u filteren op de statusobjecten van het geïmplementeerde servicepakket die zijn geretourneerd in het resultaat van geïmplementeerde toepassingsstatusquery op basis van hun status. De mogelijke waarden voor deze parameter omvatten de gehele waarde van een van de volgende statussen. Alleen geïmplementeerde servicepakketten die overeenkomen met het filter worden geretourneerd. Alle geïmplementeerde servicepakketten worden gebruikt om de geaggregeerde status van de geïmplementeerde toepassing te evalueren. Als dit niet is opgegeven, worden alle items geretourneerd. De statuswaarden zijn op vlag gebaseerde opsomming, zodat de waarde een combinatie van deze waarden kan zijn, verkregen met behulp van de bitwise 'OR'-operator. Als de opgegeven waarde bijvoorbeeld 6 is, worden de status van servicepakketten met healthstate-waarde van OK (2) en Waarschuwing (4) geretourneerd.  <br> - Standaard - Standaardwaarde. Komt overeen met elke HealthState. De waarde is nul.  <br> - Geen - Filter dat niet overeenkomt met een HealthState-waarde. Gebruikt om geen resultaten op een bepaalde verzameling van staten terug te keren. De waarde is 1.  <br> - Ok - Filter dat overeenkomt met invoer met HealthState-waarde Ok. De waarde is 2.  <br> - Waarschuwing - Filter dat overeenkomt met invoer met Waarschuwing voor de waarde HealthState. De waarde is 4.  <br> - Fout - Filter dat overeenkomt met invoer met Statuswaardefout. De waarde is 8.  <br> - Alles - Filter dat overeenkomt met de invoer met een healthstate-waarde. De waarde is 65535. |
| --events-health-state-filter | Hiermee u de verzameling HealthEvent-objecten filteren die zijn geretourneerd op basis van de status. De mogelijke waarden voor deze parameter omvatten de gehele waarde van een van de volgende statussen. Alleen gebeurtenissen die overeenkomen met het filter worden geretourneerd. Alle gebeurtenissen worden gebruikt om de geaggregeerde status te evalueren. Als dit niet is opgegeven, worden alle items geretourneerd. De statuswaarden zijn op vlag gebaseerde opsomming, dus de waarde kan een combinatie zijn van deze waarden, verkregen met behulp van de bitwise 'OR'-operator. Als de opgegeven waarde bijvoorbeeld 6 is, worden alle gebeurtenissen met de statuswaarde Van OK (2) en Waarschuwing (4) geretourneerd.  <br> - Standaard - Standaardwaarde. Komt overeen met elke HealthState. De waarde is nul.  <br> - Geen - Filter dat niet overeenkomt met een HealthState-waarde. Gebruikt om geen resultaten op een bepaalde verzameling van staten terug te keren. De waarde is 1.  <br> - Ok - Filter dat overeenkomt met invoer met HealthState-waarde Ok. De waarde is 2.  <br> - Waarschuwing - Filter dat overeenkomt met invoer met Waarschuwing voor de waarde HealthState. De waarde is 4.  <br> - Fout - Filter dat overeenkomt met invoer met Statuswaardefout. De waarde is 8.  <br> - Alles - Filter dat overeenkomt met de invoer met een healthstate-waarde. De waarde is 65535. |
| --exclude-health-statistics | Geeft aan of de statusstatistieken moeten worden geretourneerd als onderdeel van het queryresultaat. Standaard onwaar. De statistieken tonen het aantal kinderen entiteiten in de gezondheidstoestand Ok, Waarschuwing en Fout. |
| --time-out -t | De time-out van de server voor het uitvoeren van de bewerking in enkele seconden. Deze time-out geeft de tijdsduur aan die de client bereid is te wachten tot de gevraagde bewerking is voltooid. De standaardwaarde voor deze parameter is 60 seconden.  Standaard\: 60. |

### <a name="global-arguments"></a>Globale argumenten

|Argument|Beschrijving|
| --- | --- |
| --foutopsporing | Verhoog de logboekregistratie om alle foutopsporingslogboeken weer te geven. |
| --help -h | Dit helpbericht weergeven en afsluiten. |
| --output -o | Uitvoerindeling.  Toegestane waarden\: json, jsonc, tabel, tsv.  Standaard\: json. |
| --query | JMESPath-querytekenreeks. Zie\:http -jmespath.org/ voor meer informatie en voorbeelden. |
| --verbose | Verhoog de houtkap. Gebruik --debug voor volledige foutopsporingslogboeken. |

## <a name="sfctl-application-deployed-list"></a>sfctl-toepassings geïmplementeerd-lijst
Hier wordt de lijst met toepassingen geïmplementeerd op een Service Fabric-knooppunt.

Hier wordt de lijst met toepassingen geïmplementeerd op een Service Fabric-knooppunt. De resultaten bevatten geen informatie over geïmplementeerde systeemtoepassingen, tenzij expliciet gevraagd door id. De resultaten omvatten geïmplementeerde toepassingen in actieve, activerende en downloadstatussen. Deze query vereist dat de naam van het knooppunt overeenkomt met een knooppunt in het cluster. De query mislukt als de opgegeven knooppuntnaam niet wijst op actieve Servicefabric-knooppunten in het cluster.

### <a name="arguments"></a>Argumenten

|Argument|Beschrijving|
| --- | --- |
| --node-name [Vereist] | De naam van het knooppunt. |
| --continuation-token | De parameter vervolgtoken wordt gebruikt om volgende set resultaten te verkrijgen. Een vervolgtoken met een niet-lege waarde wordt opgenomen in de reactie van de API wanneer de resultaten van het systeem niet in één reactie passen. Wanneer deze waarde wordt doorgegeven aan de volgende API-aanroep, retourneert de API de volgende set resultaten. Als er geen verdere resultaten zijn, bevat het vervolgtoken geen waarde. De waarde van deze parameter mag niet worden gecodeerd met url's. |
| --include-health-state | De status van een entiteit opnemen. Als deze parameter onjuist is of niet is opgegeven, is de geretourneerde status 'Onbekend'. Wanneer deze is ingesteld op true, gaat de query parallel aan het knooppunt en de statusservice voordat de resultaten worden samengevoegd. Als gevolg hiervan is de query duurder en kan het langer duren. |
| --max-resultaten | Het maximum aantal resultaten dat moet worden geretourneerd als onderdeel van de paginaquery's. Deze parameter definieert de bovengrens op het aantal geretourneerde resultaten. De geretourneerde resultaten kunnen lager zijn dan de opgegeven maximale resultaten als ze niet in het bericht passen volgens de maximale beperkingen voor de grootte van het bericht die in de configuratie zijn gedefinieerd. Als deze parameter nul is of niet is opgegeven, bevat de paginaquery zoveel mogelijk resultaten die in het retourbericht passen. |
| --time-out -t | De time-out van de server voor het uitvoeren van de bewerking in enkele seconden. Deze time-out geeft de tijdsduur aan die de client bereid is te wachten tot de gevraagde bewerking is voltooid. De standaardwaarde voor deze parameter is 60 seconden.  Standaard\: 60. |

### <a name="global-arguments"></a>Globale argumenten

|Argument|Beschrijving|
| --- | --- |
| --foutopsporing | Verhoog de logboekregistratie om alle foutopsporingslogboeken weer te geven. |
| --help -h | Dit helpbericht weergeven en afsluiten. |
| --output -o | Uitvoerindeling.  Toegestane waarden\: json, jsonc, tabel, tsv.  Standaard\: json. |
| --query | JMESPath-querytekenreeks. Zie\:http -jmespath.org/ voor meer informatie en voorbeelden. |
| --verbose | Verhoog de houtkap. Gebruik --debug voor volledige foutopsporingslogboeken. |

## <a name="sfctl-application-health"></a>sfctl applicatie status
Krijgt de status van de service fabric applicatie.

Geeft als resultaat de heidestatus van de toepassing van de servicefabric. Het antwoord rapporteert ok, fout of waarschuwing status. Als de entiteit niet wordt gevonden in het statusarchief, wordt fout teruggezien.

### <a name="arguments"></a>Argumenten

|Argument|Beschrijving|
| --- | --- |
| --application-id [Vereist] | De identiteit van de applicatie. Dit is meestal de volledige naam van\:de toepassing zonder de 'stof ' URI regeling. Vanaf versie 6.0 worden hiërarchische namen\~afgebakend met het " teken" Als de toepassingsnaam bijvoorbeeld "fabric\:/myapp/app1" is, is\~de identiteit van de toepassing "myapp app1" in 6.0+ en "myapp/app1" in eerdere versies. |
| --ployed-applications-health-state-filter | Hiermee u filteren op de geïmplementeerde toepassingen statusobjecten die zijn geretourneerd in het resultaat van de toepassingsstatusquery op basis van hun status. De mogelijke waarden voor deze parameter omvatten de gehele waarde van een van de volgende statussen. Alleen geïmplementeerde toepassingen die overeenkomen met het filter worden geretourneerd. Alle geïmplementeerde toepassingen worden gebruikt om de geaggregeerde status te evalueren. Als dit niet is opgegeven, worden alle items geretourneerd. De statuswaarden zijn op vlag gebaseerde opsomming, dus de waarde kan een combinatie zijn van deze waarden, verkregen met bitwise 'OR'-operator. Als de opgegeven waarde bijvoorbeeld 6 is, wordt de status van geïmplementeerde toepassingen met de status HealthState van OK (2) en Waarschuwing (4) geretourneerd.  <br> - Standaard - Standaardwaarde. Komt overeen met elke HealthState. De waarde is nul.  <br> - Geen - Filter dat niet overeenkomt met een HealthState-waarde. Gebruikt om geen resultaten op een bepaalde verzameling van staten terug te keren. De waarde is 1.  <br> - Ok - Filter dat overeenkomt met invoer met HealthState-waarde Ok. De waarde is 2.  <br> - Waarschuwing - Filter dat overeenkomt met invoer met Waarschuwing voor de waarde HealthState. De waarde is 4.  <br> - Fout - Filter dat overeenkomt met invoer met Statuswaardefout. De waarde is 8.  <br> - Alles - Filter dat overeenkomt met de invoer met een healthstate-waarde. De waarde is 65535. |
| --events-health-state-filter | Hiermee u de verzameling HealthEvent-objecten filteren die zijn geretourneerd op basis van de status. De mogelijke waarden voor deze parameter omvatten de gehele waarde van een van de volgende statussen. Alleen gebeurtenissen die overeenkomen met het filter worden geretourneerd. Alle gebeurtenissen worden gebruikt om de geaggregeerde status te evalueren. Als dit niet is opgegeven, worden alle items geretourneerd. De statuswaarden zijn op vlag gebaseerde opsomming, dus de waarde kan een combinatie zijn van deze waarden, verkregen met behulp van de bitwise 'OR'-operator. Als de opgegeven waarde bijvoorbeeld 6 is, worden alle gebeurtenissen met de statuswaarde Van OK (2) en Waarschuwing (4) geretourneerd.  <br> - Standaard - Standaardwaarde. Komt overeen met elke HealthState. De waarde is nul.  <br> - Geen - Filter dat niet overeenkomt met een HealthState-waarde. Gebruikt om geen resultaten op een bepaalde verzameling van staten terug te keren. De waarde is 1.  <br> - Ok - Filter dat overeenkomt met invoer met HealthState-waarde Ok. De waarde is 2.  <br> - Waarschuwing - Filter dat overeenkomt met invoer met Waarschuwing voor de waarde HealthState. De waarde is 4.  <br> - Fout - Filter dat overeenkomt met invoer met Statuswaardefout. De waarde is 8.  <br> - Alles - Filter dat overeenkomt met de invoer met een healthstate-waarde. De waarde is 65535. |
| --exclude-health-statistics | Geeft aan of de statusstatistieken moeten worden geretourneerd als onderdeel van het queryresultaat. Standaard onwaar. De statistieken tonen het aantal kinderen entiteiten in de gezondheidstoestand Ok, Waarschuwing en Fout. |
| --services-health-state-filter | Hiermee u filteren op de statusobjecten van services die zijn geretourneerd naar aanleiding van de statusquery van services op basis van hun status. De mogelijke waarden voor deze parameter omvatten de gehele waarde van een van de volgende statussen. Alleen services die overeenkomen met het filter worden geretourneerd. Alle services worden gebruikt om de geaggregeerde status te evalueren. Als dit niet is opgegeven, worden alle items geretourneerd. De statuswaarden zijn op vlag gebaseerde opsomming, dus de waarde kan een combinatie zijn van deze waarden, verkregen met bitwise 'OR'-operator. Als de opgegeven waarde bijvoorbeeld 6 is, wordt de status van services met healthstate-waarde van OK (2) en Waarschuwing (4) geretourneerd.  <br> - Standaard - Standaardwaarde. Komt overeen met elke HealthState. De waarde is nul.  <br> - Geen - Filter dat niet overeenkomt met een HealthState-waarde. Gebruikt om geen resultaten op een bepaalde verzameling van staten terug te keren. De waarde is 1.  <br> - Ok - Filter dat overeenkomt met invoer met HealthState-waarde Ok. De waarde is 2.  <br> - Waarschuwing - Filter dat overeenkomt met invoer met Waarschuwing voor de waarde HealthState. De waarde is 4.  <br> - Fout - Filter dat overeenkomt met invoer met Statuswaardefout. De waarde is 8.  <br> - Alles - Filter dat overeenkomt met de invoer met een healthstate-waarde. De waarde is 65535. |
| --time-out -t | De time-out van de server voor het uitvoeren van de bewerking in enkele seconden. Deze time-out geeft de tijdsduur aan die de client bereid is te wachten tot de gevraagde bewerking is voltooid. De standaardwaarde voor deze parameter is 60 seconden.  Standaard\: 60. |

### <a name="global-arguments"></a>Globale argumenten

|Argument|Beschrijving|
| --- | --- |
| --foutopsporing | Verhoog de logboekregistratie om alle foutopsporingslogboeken weer te geven. |
| --help -h | Dit helpbericht weergeven en afsluiten. |
| --output -o | Uitvoerindeling.  Toegestane waarden\: json, jsonc, tabel, tsv.  Standaard\: json. |
| --query | JMESPath-querytekenreeks. Zie\:http -jmespath.org/ voor meer informatie en voorbeelden. |
| --verbose | Verhoog de houtkap. Gebruik --debug voor volledige foutopsporingslogboeken. |

## <a name="sfctl-application-info"></a>sfctl applicatie-informatie
Hier vindt u informatie over een Service Fabric-toepassing.

Geeft als resultaat de informatie over de toepassing die is gemaakt of die wordt gemaakt in het cluster Servicefabric en waarvan de naam overeenkomt met de toepassing die is opgegeven als de parameter. Het antwoord bevat de naam, het type, de status, de parameters en andere details over de toepassing.

### <a name="arguments"></a>Argumenten

|Argument|Beschrijving|
| --- | --- |
| --application-id [Vereist] | De identiteit van de applicatie. Dit is meestal de volledige naam van\:de toepassing zonder de 'stof ' URI regeling. Vanaf versie 6.0 worden hiërarchische namen\~afgebakend met het " teken" Als de toepassingsnaam bijvoorbeeld "fabric\:/myapp/app1" is, is\~de identiteit van de toepassing "myapp app1" in 6.0+ en "myapp/app1" in eerdere versies. |
| --exclude-application-parameters | De vlag die aangeeft of toepassingsparameters worden uitgesloten van het resultaat. |
| --time-out -t | De time-out van de server voor het uitvoeren van de bewerking in enkele seconden. Deze time-out geeft de tijdsduur aan die de client bereid is te wachten tot de gevraagde bewerking is voltooid. De standaardwaarde voor deze parameter is 60 seconden.  Standaard\: 60. |

### <a name="global-arguments"></a>Globale argumenten

|Argument|Beschrijving|
| --- | --- |
| --foutopsporing | Verhoog de logboekregistratie om alle foutopsporingslogboeken weer te geven. |
| --help -h | Dit helpbericht weergeven en afsluiten. |
| --output -o | Uitvoerindeling.  Toegestane waarden\: json, jsonc, tabel, tsv.  Standaard\: json. |
| --query | JMESPath-querytekenreeks. Zie\:http -jmespath.org/ voor meer informatie en voorbeelden. |
| --verbose | Verhoog de houtkap. Gebruik --debug voor volledige foutopsporingslogboeken. |

## <a name="sfctl-application-list"></a>sfctl-toepassingslijst
Hiermee wordt de lijst met toepassingen weergegeven die zijn gemaakt in het cluster Servicefabric die overeenkomen met de opgegeven filters.

Hiermee krijgt u de informatie over de toepassingen die zijn gemaakt of die worden gemaakt in het cluster Servicefabric en overeenkomen met de opgegeven filters. Het antwoord bevat de naam, het type, de status, de parameters en andere details over de toepassing. Als de toepassingen niet in een pagina passen, wordt één pagina met resultaten geretourneerd, evenals een vervolgtoken, dat kan worden gebruikt om de volgende pagina te krijgen. Filters ApplicationTypeName en ApplicationDefinitionKindFilter kunnen niet tegelijkertijd worden opgegeven.

### <a name="arguments"></a>Argumenten

|Argument|Beschrijving|
| --- | --- |
| --application-definition-kind-filter | Wordt gebruikt om te filteren op ApplicationDefinitionKind, het mechanisme dat wordt gebruikt om een Service Fabric-toepassing te definiëren.  <br> - Standaard - Standaardwaarde, die dezelfde functie vervult als het selecteren van "Alles". De waarde is 0.  <br> - Alles - Filter dat overeenkomt met de invoer met een ApplicationDefinitionKind-waarde. De waarde is 65535.  <br> - ServiceFabricApplicationDescription - Filter dat overeenkomt met de invoer met ApplicationDefinitionKind-waarde ServiceFabricApplicationDescription. De waarde is 1.  <br> - Componeren - Filter dat overeenkomt met de invoer met ApplicationDefinitionKind-waarde Compose. De waarde is 2. |
| --application-type-name | De naam van het toepassingstype dat wordt gebruikt om de toepassingen te filteren waarvoor u wilt zoeken. Deze waarde mag de versie van het toepassingstype niet bevatten. |
| --continuation-token | De parameter vervolgtoken wordt gebruikt om volgende set resultaten te verkrijgen. Een vervolgtoken met een niet-lege waarde wordt opgenomen in de reactie van de API wanneer de resultaten van het systeem niet in één reactie passen. Wanneer deze waarde wordt doorgegeven aan de volgende API-aanroep, retourneert de API de volgende set resultaten. Als er geen verdere resultaten zijn, bevat het vervolgtoken geen waarde. De waarde van deze parameter mag niet worden gecodeerd met url's. |
| --exclude-application-parameters | De vlag die aangeeft of toepassingsparameters worden uitgesloten van het resultaat. |
| --max-resultaten | Het maximum aantal resultaten dat moet worden geretourneerd als onderdeel van de paginaquery's. Deze parameter definieert de bovengrens op het aantal geretourneerde resultaten. De geretourneerde resultaten kunnen lager zijn dan de opgegeven maximale resultaten als ze niet in het bericht passen volgens de maximale beperkingen voor de grootte van het bericht die in de configuratie zijn gedefinieerd. Als deze parameter nul is of niet is opgegeven, bevat de paginaquery zoveel mogelijk resultaten die in het retourbericht passen. |
| --time-out -t | De time-out van de server voor het uitvoeren van de bewerking in enkele seconden. Deze time-out geeft de tijdsduur aan die de client bereid is te wachten tot de gevraagde bewerking is voltooid. De standaardwaarde voor deze parameter is 60 seconden.  Standaard\: 60. |

### <a name="global-arguments"></a>Globale argumenten

|Argument|Beschrijving|
| --- | --- |
| --foutopsporing | Verhoog de logboekregistratie om alle foutopsporingslogboeken weer te geven. |
| --help -h | Dit helpbericht weergeven en afsluiten. |
| --output -o | Uitvoerindeling.  Toegestane waarden\: json, jsonc, tabel, tsv.  Standaard\: json. |
| --query | JMESPath-querytekenreeks. Zie\:http -jmespath.org/ voor meer informatie en voorbeelden. |
| --verbose | Verhoog de houtkap. Gebruik --debug voor volledige foutopsporingslogboeken. |

## <a name="sfctl-application-load"></a>sfctl-toepassingsbelasting
Hier vindt u informatie over het laden over een Service Fabric-toepassing.

Geeft als resultaat de belastingsinformatie over de toepassing die is gemaakt of die wordt gemaakt in het cluster Servicefabric en waarvan de naam overeenkomt met de toepassing die is opgegeven als de parameter. Het antwoord omvat de naam, minimale knooppunten, maximale knooppunten, het aantal knooppunten dat de toepassing momenteel bezet, en de toepassing laden metrische informatie over de toepassing.

### <a name="arguments"></a>Argumenten

|Argument|Beschrijving|
| --- | --- |
| --application-id [Vereist] | De identiteit van de applicatie. Dit is meestal de volledige naam van\:de toepassing zonder de 'stof ' URI regeling. Vanaf versie 6.0 worden hiërarchische namen\~afgebakend met het " teken" Als de toepassingsnaam bijvoorbeeld "fabric\:/myapp/app1" is, is\~de identiteit van de toepassing "myapp app1" in 6.0+ en "myapp/app1" in eerdere versies. |
| --time-out -t | De time-out van de server voor het uitvoeren van de bewerking in enkele seconden. Deze time-out geeft de tijdsduur aan die de client bereid is te wachten tot de gevraagde bewerking is voltooid. De standaardwaarde voor deze parameter is 60 seconden.  Standaard\: 60. |

### <a name="global-arguments"></a>Globale argumenten

|Argument|Beschrijving|
| --- | --- |
| --foutopsporing | Verhoog de logboekregistratie om alle foutopsporingslogboeken weer te geven. |
| --help -h | Dit helpbericht weergeven en afsluiten. |
| --output -o | Uitvoerindeling.  Toegestane waarden\: json, jsonc, tabel, tsv.  Standaard\: json. |
| --query | JMESPath-querytekenreeks. Zie\:http -jmespath.org/ voor meer informatie en voorbeelden. |
| --verbose | Verhoog de houtkap. Gebruik --debug voor volledige foutopsporingslogboeken. |

## <a name="sfctl-application-manifest"></a>sfctl-aanvraagmanifest
Hier wordt het manifest geplaatst waarin een toepassingstype wordt beschreven.

Het antwoord bevat xml als tekenreeks als toepassingsmanifest.

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

## <a name="sfctl-application-provision"></a>sfctl-toepassingsbepaling
Voorziet of registreert een Service Fabric-toepassingstype met het cluster met behulp van het pakket '.sfpkg' in de externe winkel of met behulp van het toepassingspakket in de afbeeldingswinkel.

Voorziet een servicefabric-toepassingstype met het cluster. De bepaling is vereist voordat nieuwe toepassingen kunnen worden geinstantieerd. De bepalingsbewerking kan worden uitgevoerd op het toepassingspakket dat is opgegeven door de relatieve PathInImageStore, of met behulp van de URI van de externe '.sfpkg'. Tenzij --externe voorziening is ingesteld, verwacht deze opdracht de voorziening voor afbeeldingsopslag.

### <a name="arguments"></a>Argumenten

|Argument|Beschrijving|
| --- | --- |
| --applicatie-pakket-download-uri | Het pad naar het '.sfpkg' applicatiepakket van waaruit het applicatiepakket kan worden gedownload met BEHULP van HTTP- of HTTPS-protocollen. <br><br> Voor de voorziening soort externe winkel alleen. Het toepassingspakket kan worden opgeslagen in een externe winkel die GET-bewerking biedt om het bestand te downloaden. Ondersteunde protocollen zijn HTTP en HTTPS en het pad moet LEES-toegang toestaan. |
| --application-type-build-path | Voor bepaling soort beeld op te slaan alleen. Het relatieve pad voor het toepassingspakket in het afbeeldingsarchief dat is opgegeven tijdens de eerdere uploadbewerking. |
| --application-type-name | Voor de voorziening soort externe winkel alleen. De naam van het toepassingstype vertegenwoordigt de naam van het toepassingstype in het toepassingsmanifest. |
| --application-type-versie | Voor de voorziening soort externe winkel alleen. De versie van het toepassingstype vertegenwoordigt de versie van het toepassingstype in het toepassingsmanifest. |
| --externe voorziening | De locatie van waaruit het aanvraagpakket kan worden geregistreerd of ingericht. Geeft aan dat de voorziening is voor een toepassingspakket dat eerder naar een extern archief is geüpload. Het applicatiepakket eindigt met de extensie *.sfpkg. |
| --no-wait | Geeft aan of provisioning asynchroon moet plaatsvinden. <br><br> Wanneer ingesteld op true, de bepaling operatie keert terug wanneer de aanvraag wordt aanvaard door het systeem, en de bepaling operatie wordt voortgezet zonder enige time-out limiet. De standaardwaarde is false. Voor grote toepassingspakketten raden we aan de waarde in te stellen op true. |
| --time-out -t | Standaard\: 60. |

### <a name="global-arguments"></a>Globale argumenten

|Argument|Beschrijving|
| --- | --- |
| --foutopsporing | Verhoog de logboekregistratie om alle foutopsporingslogboeken weer te geven. |
| --help -h | Dit helpbericht weergeven en afsluiten. |
| --output -o | Uitvoerindeling.  Toegestane waarden\: json, jsonc, tabel, tsv.  Standaard\: json. |
| --query | JMESPath-querytekenreeks. Zie\:http -jmespath.org/ voor meer informatie en voorbeelden. |
| --verbose | Verhoog de houtkap. Gebruik --debug voor volledige foutopsporingslogboeken. |

## <a name="sfctl-application-report-health"></a>sfctl applicatie report-health
Hiermee verzendt u een gezondheidsrapport over de toepassing Service Fabric.

Rapporteert de status van de opgegeven Service Fabric-toepassing. Het rapport moet de informatie bevatten over de bron van het gezondheidsrapport en de eigenschap waarop het wordt gerapporteerd. Het rapport wordt verzonden naar een Service Fabric-gatewaytoepassing, die doorstuurt naar de statuswinkel. Het rapport kan worden geaccepteerd door de gateway, maar afgewezen door het gezondheidsarchief na extra validatie. Het statusarchief kan het rapport bijvoorbeeld afwijzen vanwege een ongeldige parameter, zoals een verouderd volgnummer. Als u wilt zien of het rapport is toegepast in het gezondheidsarchief, krijgt u de status van de toepassing en controleert u of het rapport wordt weergegeven.

### <a name="arguments"></a>Argumenten

|Argument|Beschrijving|
| --- | --- |
| --application-id [Vereist] | De identiteit van de applicatie. <br><br> Dit is meestal de volledige naam van\:de toepassing zonder de 'stof ' URI regeling. Vanaf versie 6.0 worden hiërarchische namen\~afgebakend met het ' teken.' Als de toepassingsnaam bijvoorbeeld 'fabric\:/myapp/app1' is, is de\~toepassingsidentiteit 'myapp app1' in 6.0+ en 'myapp/app1' in eerdere versies. |
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

## <a name="sfctl-application-type"></a>sfctl-toepassingstype
Hier wordt de lijst met toepassingstypen in het cluster Servicefabric weergegeven dat exact overeenkomt met de opgegeven naam.

Geeft als resultaat de informatie over de toepassingstypen die zijn ingericht of die worden ingericht in het cluster Servicefabric. Deze resultaten zijn van toepassingstypen waarvan de naam exact overeenkomt met de parameter en die voldoen aan de opgegeven queryparameters. Alle versies van het toepassingstype dat overeenkomt met de naam van het toepassingstype worden geretourneerd, waarbij elke versie wordt geretourneerd als één toepassingstype. Het antwoord bevat de naam, versie, status en andere details over het toepassingstype. Dit is een opgepiepte query, wat betekent dat als niet alle toepassingstypen in een pagina passen, één pagina met resultaten wordt geretourneerd, evenals een vervolgtoken, dat kan worden gebruikt om de volgende pagina te krijgen. Als er bijvoorbeeld 10 toepassingstypen zijn, maar een pagina alleen past bij de eerste drie toepassingstypen of als de maximale resultaten zijn ingesteld op 3, worden er drie geretourneerd. Als u de rest van de resultaten wilt openen, haalt u de volgende pagina's op met behulp van het geretourneerde vervolgtoken in de volgende query. Een leeg vervolgtoken wordt geretourneerd als er geen volgende pagina's zijn.

### <a name="arguments"></a>Argumenten

|Argument|Beschrijving|
| --- | --- |
| --application-type-name [Vereist] | De naam van het toepassingstype. |
| --application-type-versie | De versie van het toepassingstype. |
| --continuation-token | De parameter vervolgtoken wordt gebruikt om volgende set resultaten te verkrijgen. Een vervolgtoken met een niet-lege waarde wordt opgenomen in de reactie van de API wanneer de resultaten van het systeem niet in één reactie passen. Wanneer deze waarde wordt doorgegeven aan de volgende API-aanroep, retourneert de API de volgende set resultaten. Als er geen verdere resultaten zijn, bevat het vervolgtoken geen waarde. De waarde van deze parameter mag niet worden gecodeerd met url's. |
| --exclude-application-parameters | De vlag die aangeeft of toepassingsparameters worden uitgesloten van het resultaat. |
| --max-resultaten | Het maximum aantal resultaten dat moet worden geretourneerd als onderdeel van de paginaquery's. Deze parameter definieert de bovengrens op het aantal geretourneerde resultaten. De geretourneerde resultaten kunnen lager zijn dan de opgegeven maximale resultaten als ze niet in het bericht passen volgens de maximale beperkingen voor de grootte van het bericht die in de configuratie zijn gedefinieerd. Als deze parameter nul is of niet is opgegeven, bevat de paginaquery zoveel mogelijk resultaten die in het retourbericht passen. |
| --time-out -t | De time-out van de server voor het uitvoeren van de bewerking in enkele seconden. Deze time-out geeft de tijdsduur aan die de client bereid is te wachten tot de gevraagde bewerking is voltooid. De standaardwaarde voor deze parameter is 60 seconden.  Standaard\: 60. |

### <a name="global-arguments"></a>Globale argumenten

|Argument|Beschrijving|
| --- | --- |
| --foutopsporing | Verhoog de logboekregistratie om alle foutopsporingslogboeken weer te geven. |
| --help -h | Dit helpbericht weergeven en afsluiten. |
| --output -o | Uitvoerindeling.  Toegestane waarden\: json, jsonc, tabel, tsv.  Standaard\: json. |
| --query | JMESPath-querytekenreeks. Zie\:http -jmespath.org/ voor meer informatie en voorbeelden. |
| --verbose | Verhoog de houtkap. Gebruik --debug voor volledige foutopsporingslogboeken. |

## <a name="sfctl-application-type-list"></a>sfctl-lijst met toepassingstypen
Hier wordt de lijst met toepassingstypen weergegeven in het cluster Servicefabric.

Geeft als resultaat de informatie over de toepassingstypen die zijn ingericht of die worden ingericht in het cluster Servicefabric. Elke versie van een toepassingstype wordt geretourneerd als één toepassingstype. Het antwoord bevat de naam, versie, status en andere details over het toepassingstype. Dit is een opgepiepte query, wat betekent dat als niet alle toepassingstypen in een pagina passen, één pagina met resultaten wordt geretourneerd, evenals een vervolgtoken, dat kan worden gebruikt om de volgende pagina te krijgen. Als er bijvoorbeeld 10 toepassingstypen zijn, maar een pagina alleen past bij de eerste drie toepassingstypen of als de maximale resultaten zijn ingesteld op 3, worden er drie geretourneerd. Als u de rest van de resultaten wilt openen, haalt u de volgende pagina's op met behulp van het geretourneerde vervolgtoken in de volgende query. Een leeg vervolgtoken wordt geretourneerd als er geen volgende pagina's zijn.

### <a name="arguments"></a>Argumenten

|Argument|Beschrijving|
| --- | --- |
| --application-type-definition-kind-filter | Wordt gebruikt om te filteren op ApplicationTypeDefinitionKind, het mechanisme dat wordt gebruikt om een servicefabric-toepassingstype te definiëren.  <br> - Standaard - Standaardwaarde, die dezelfde functie vervult als het selecteren van "Alles". De waarde is 0.  <br> - Alles - Filter dat overeenkomt met de invoer met een ApplicationTypeDefinitionKind-waarde. De waarde is 65535.  <br> - ServiceFabricApplicationPackage - Filter dat overeenkomt met invoer met ApplicationTypeDefinitionKind-waarde ServiceFabricApplicationPackage. De waarde is 1.  <br> - Componeren - Filter dat overeenkomt met de invoer met de waarde Compose van ApplicationTypeDefinitionKind. De waarde is 2. |
| --continuation-token | De parameter vervolgtoken wordt gebruikt om volgende set resultaten te verkrijgen. Een vervolgtoken met een niet-lege waarde wordt opgenomen in de reactie van de API wanneer de resultaten van het systeem niet in één reactie passen. Wanneer deze waarde wordt doorgegeven aan de volgende API-aanroep, retourneert de API de volgende set resultaten. Als er geen verdere resultaten zijn, bevat het vervolgtoken geen waarde. De waarde van deze parameter mag niet worden gecodeerd met url's. |
| --exclude-application-parameters | De vlag die aangeeft of toepassingsparameters worden uitgesloten van het resultaat. |
| --max-resultaten | Het maximum aantal resultaten dat moet worden geretourneerd als onderdeel van de paginaquery's. Deze parameter definieert de bovengrens op het aantal geretourneerde resultaten. De geretourneerde resultaten kunnen lager zijn dan de opgegeven maximale resultaten als ze niet in het bericht passen volgens de maximale beperkingen voor de grootte van het bericht die in de configuratie zijn gedefinieerd. Als deze parameter nul is of niet is opgegeven, bevat de paginaquery zoveel mogelijk resultaten die in het retourbericht passen. |
| --time-out -t | De time-out van de server voor het uitvoeren van de bewerking in enkele seconden. Deze time-out geeft de tijdsduur aan die de client bereid is te wachten tot de gevraagde bewerking is voltooid. De standaardwaarde voor deze parameter is 60 seconden.  Standaard\: 60. |

### <a name="global-arguments"></a>Globale argumenten

|Argument|Beschrijving|
| --- | --- |
| --foutopsporing | Verhoog de logboekregistratie om alle foutopsporingslogboeken weer te geven. |
| --help -h | Dit helpbericht weergeven en afsluiten. |
| --output -o | Uitvoerindeling.  Toegestane waarden\: json, jsonc, tabel, tsv.  Standaard\: json. |
| --query | JMESPath-querytekenreeks. Zie\:http -jmespath.org/ voor meer informatie en voorbeelden. |
| --verbose | Verhoog de houtkap. Gebruik --debug voor volledige foutopsporingslogboeken. |

## <a name="sfctl-application-unprovision"></a>sfctl-toepassing ontviand
Hiermee verwijdert of verwijdert u een servicefabric-toepassingstype uit het cluster.

Deze bewerking kan alleen worden uitgevoerd als alle toepassingsinstanties van het toepassingstype zijn verwijderd. Zodra het toepassingstype is niet geregistreerd, kunnen er geen nieuwe toepassingsinstanties worden gemaakt voor dit specifieke toepassingstype.

### <a name="arguments"></a>Argumenten

|Argument|Beschrijving|
| --- | --- |
| --application-type-name [Vereist] | De naam van het toepassingstype. |
| --application-type-versie [Vereist] | De versie van het toepassingstype zoals gedefinieerd in het toepassingsmanifest. |
| --async-parameter | De vlag die aangeeft of de voorziening al dan niet asynchroon moet worden uitgevoerd. Wanneer de voorzieningsbewerking is ingesteld, wordt de ontregelingsbewerking geretourneerd wanneer de aanvraag door het systeem wordt geaccepteerd en wordt de ontregelingsbewerking zonder enige time-outlimiet voortgezet. De standaardwaarde is false. We raden u echter aan deze in te stellen op de true voor grote toepassingspakketten die zijn ingericht. |
| --time-out -t | De time-out van de server voor het uitvoeren van de bewerking in enkele seconden. Deze time-out geeft de tijdsduur aan die de client bereid is te wachten tot de gevraagde bewerking is voltooid. De standaardwaarde voor deze parameter is 60 seconden.  Standaard\: 60. |

### <a name="global-arguments"></a>Globale argumenten

|Argument|Beschrijving|
| --- | --- |
| --foutopsporing | Verhoog de logboekregistratie om alle foutopsporingslogboeken weer te geven. |
| --help -h | Dit helpbericht weergeven en afsluiten. |
| --output -o | Uitvoerindeling.  Toegestane waarden\: json, jsonc, tabel, tsv.  Standaard\: json. |
| --query | JMESPath-querytekenreeks. Zie\:http -jmespath.org/ voor meer informatie en voorbeelden. |
| --verbose | Verhoog de houtkap. Gebruik --debug voor volledige foutopsporingslogboeken. |

## <a name="sfctl-application-upgrade"></a>sfctl applicatie-upgrade
Hiermee wordt gestart met het upgraden van een toepassing in het cluster Servicefabric.

Valideert de opgegeven upgradeparameters voor toepassingen en start het upgraden van de toepassing als de parameters geldig zijn. Houd er rekening mee dat de upgradebeschrijving de bestaande toepassingsbeschrijving vervangt. Dit betekent dat als de parameters niet zijn opgegeven, de bestaande parameters op de toepassingen worden overschreven met de lege parameterslijst. Dit zou ertoe leiden dat de toepassing de standaardwaarde van de parameters uit het toepassingsmanifest gebruikt.

### <a name="arguments"></a>Argumenten

|Argument|Beschrijving|
| --- | --- |
| --application-id [Vereist] | De identiteit van de applicatie. <br><br> Dit is meestal de volledige naam van\:de toepassing zonder de 'stof ' URI regeling. Vanaf versie 6.0 worden hiërarchische namen\~afgebakend met het " teken" Als de toepassingsnaam bijvoorbeeld "fabric\:/myapp/app1" is, is\~de identiteit van de toepassing "myapp app1" in 6.0+ en "myapp/app1" in eerdere versies. |
| --application-versie [Vereist] | De doelversie van het toepassingstype (gevonden in het toepassingsmanifest) voor de toepassingsverbetering. |
| --parameters [Vereist] | Een JSON gecodeerde lijst van toepassingsparameteroverschrijvingen die moeten worden toegepast bij het upgraden van de toepassing. |
| --default-service-health-policy | Json gecodeerde specificatie van het gezondheidsbeleid dat standaard wordt gebruikt om de status van een servicetype te evalueren. |
| --failure-action | De actie die moet worden uitgevoerd wanneer een bewaakte upgrade wordt geconfronteerd met schendingen van het beleid of schendingen van het gezondheidsbeleid. |
| --force-herstart | Processen krachtig opnieuw opstarten tijdens de upgrade, zelfs als de codeversie niet is gewijzigd. |
| --health-check-retry-time-out | De tijdsduur tussen pogingen om statuscontroles uit te voeren als de toepassing of het cluster niet in orde is.  Standaard\: PT0H10M0S. |
| --health-check-stable-duration | De hoeveelheid tijd dat de toepassing of het cluster gezond moet blijven voordat de upgrade doorgaat naar het volgende upgradedomein.  Standaard\: PT0H2M0S. <br><br> Het wordt eerst geïnterpreteerd als een tekenreeks die een ISO 8601-duur vertegenwoordigt. Als dat niet lukt, wordt het geïnterpreteerd als een getal dat het totale aantal milliseconden vertegenwoordigt. |
| --health-check-wait-duration | De tijdsduur om te wachten na het voltooien van een upgradedomein voordat u het proces voor statuscontroles start.  Standaard\: 0. |
| --max-ongezonde apps | Het maximaal toegestane percentage ongezonde geïmplementeerde toepassingen. Vertegenwoordigd als een getal tussen 0 en 100. |
| --modus | De modus die wordt gebruikt om de gezondheid te controleren tijdens een rolling upgrade.  Standaard\: Niet-gecontroleerdAuto. |
| --replica-set-check-time-out | De maximale tijd om de verwerking van een upgradedomein te blokkeren en verlies van beschikbaarheid te voorkomen wanneer er onverwachte problemen zijn. Gemeten in seconden. |
| --service-health-policy | Json-gecodeerde kaart met servicetypestatusbeleid per servicetypenaam. De kaart is leeg zijn standaard. |
| --time-out -t | Standaard\: 60. |
| --upgrade-domein-time-out | De hoeveelheid tijd die elk upgradedomein moet voltooien voordat FailureAction wordt uitgevoerd.  Standaard\: P10675199DT02H48M05.4775807S. <br><br> Het wordt eerst geïnterpreteerd als een tekenreeks die een ISO 8601-duur vertegenwoordigt. Als dat niet lukt, wordt het geïnterpreteerd als een getal dat het totale aantal milliseconden vertegenwoordigt. |
| --upgrade-time-out | De hoeveelheid tijd die de algehele upgrade moet voltooien voordat FailureAction wordt uitgevoerd.  Standaard\: P10675199DT02H48M05.4775807S. <br><br> Het wordt eerst geïnterpreteerd als een tekenreeks die een ISO 8601-duur vertegenwoordigt. Als dat niet lukt, wordt het geïnterpreteerd als een getal dat het totale aantal milliseconden vertegenwoordigt. |
| --waarschuwing-als-fout | Geeft aan of waarschuwingen met dezelfde ernst worden behandeld als fouten. |

### <a name="global-arguments"></a>Globale argumenten

|Argument|Beschrijving|
| --- | --- |
| --foutopsporing | Verhoog de logboekregistratie om alle foutopsporingslogboeken weer te geven. |
| --help -h | Dit helpbericht weergeven en afsluiten. |
| --output -o | Uitvoerindeling.  Toegestane waarden\: json, jsonc, tabel, tsv.  Standaard\: json. |
| --query | JMESPath-querytekenreeks. Zie\:http -jmespath.org/ voor meer informatie en voorbeelden. |
| --verbose | Verhoog de houtkap. Gebruik --debug voor volledige foutopsporingslogboeken. |

## <a name="sfctl-application-upgrade-resume"></a>sfctl applicatie upgrade-cv
Hervat het upgraden van een toepassing in het cluster ServiceFabric.

Hervat een upgrade van de niet-bewaakte handmatige servicefabric-toepassing. Service Fabric upgradet één upgradedomein tegelijk. Voor niet-bewaakte handmatige upgrades wacht u, nadat Service Fabric een upgradedomein heeft voltooid, voordat u doorgaat naar het volgende upgradedomein.

### <a name="arguments"></a>Argumenten

|Argument|Beschrijving|
| --- | --- |
| --application-id [Vereist] | De identiteit van de applicatie. Dit is meestal de volledige naam van\:de toepassing zonder de 'stof ' URI regeling. Vanaf versie 6.0 worden hiërarchische namen\~afgebakend met het " teken" Als de toepassingsnaam bijvoorbeeld "fabric\:/myapp/app1" is, is\~de identiteit van de toepassing "myapp app1" in 6.0+ en "myapp/app1" in eerdere versies. |
| --upgrade-domeinnaam [Vereist] | De naam van het upgradedomein waarin de upgrade kan worden hervat. |
| --time-out -t | De time-out van de server voor het uitvoeren van de bewerking in enkele seconden. Deze time-out geeft de tijdsduur aan die de client bereid is te wachten tot de gevraagde bewerking is voltooid. De standaardwaarde voor deze parameter is 60 seconden.  Standaard\: 60. |

### <a name="global-arguments"></a>Globale argumenten

|Argument|Beschrijving|
| --- | --- |
| --foutopsporing | Verhoog de logboekregistratie om alle foutopsporingslogboeken weer te geven. |
| --help -h | Dit helpbericht weergeven en afsluiten. |
| --output -o | Uitvoerindeling.  Toegestane waarden\: json, jsonc, tabel, tsv.  Standaard\: json. |
| --query | JMESPath-querytekenreeks. Zie\:http -jmespath.org/ voor meer informatie en voorbeelden. |
| --verbose | Verhoog de houtkap. Gebruik --debug voor volledige foutopsporingslogboeken. |

## <a name="sfctl-application-upgrade-rollback"></a>sfctl applicatie upgrade-rollback
Hiermee wordt de momenteel lopende upgrade van een toepassing in het cluster Service Fabric teruggedraaid.

Hiermee wordt de huidige toepassingsupgrade naar de vorige versie teruggedraaid. Deze API kan alleen worden gebruikt om de huidige voortgangsupgrade terug te draaien die naar een nieuwe versie wordt teruggedraaid. Als de toepassing momenteel niet wordt bijgewerkt, gebruikt u de StartApplicationUpgrade API om deze te upgraden naar de gewenste versie, inclusief het terugdraaien naar een vorige versie.

### <a name="arguments"></a>Argumenten

|Argument|Beschrijving|
| --- | --- |
| --application-id [Vereist] | De identiteit van de applicatie. Dit is meestal de volledige naam van\:de toepassing zonder de 'stof ' URI regeling. Vanaf versie 6.0 worden hiërarchische namen\~afgebakend met het " teken" Als de toepassingsnaam bijvoorbeeld "fabric\:/myapp/app1" is, is\~de identiteit van de toepassing "myapp app1" in 6.0+ en "myapp/app1" in eerdere versies. |
| --time-out -t | De time-out van de server voor het uitvoeren van de bewerking in enkele seconden. Deze time-out geeft de tijdsduur aan die de client bereid is te wachten tot de gevraagde bewerking is voltooid. De standaardwaarde voor deze parameter is 60 seconden.  Standaard\: 60. |

### <a name="global-arguments"></a>Globale argumenten

|Argument|Beschrijving|
| --- | --- |
| --foutopsporing | Verhoog de logboekregistratie om alle foutopsporingslogboeken weer te geven. |
| --help -h | Dit helpbericht weergeven en afsluiten. |
| --output -o | Uitvoerindeling.  Toegestane waarden\: json, jsonc, tabel, tsv.  Standaard\: json. |
| --query | JMESPath-querytekenreeks. Zie\:http -jmespath.org/ voor meer informatie en voorbeelden. |
| --verbose | Verhoog de houtkap. Gebruik --debug voor volledige foutopsporingslogboeken. |

## <a name="sfctl-application-upgrade-status"></a>sfctl applicatie upgrade-status
Krijgt details voor de nieuwste upgrade uitgevoerd op deze applicatie.

Retourneert informatie over de status van de nieuwste toepassingsupgrade, samen met details om problemen met het opsporen van toepassingsstatus te helpen.

### <a name="arguments"></a>Argumenten

|Argument|Beschrijving|
| --- | --- |
| --application-id [Vereist] | De identiteit van de applicatie. Dit is meestal de volledige naam van\:de toepassing zonder de 'stof ' URI regeling. Vanaf versie 6.0 worden hiërarchische namen\~afgebakend met het " teken" Als de toepassingsnaam bijvoorbeeld "fabric\:/myapp/app1" is, is\~de identiteit van de toepassing "myapp app1" in 6.0+ en "myapp/app1" in eerdere versies. |
| --time-out -t | De time-out van de server voor het uitvoeren van de bewerking in enkele seconden. Deze time-out geeft de tijdsduur aan die de client bereid is te wachten tot de gevraagde bewerking is voltooid. De standaardwaarde voor deze parameter is 60 seconden.  Standaard\: 60. |

### <a name="global-arguments"></a>Globale argumenten

|Argument|Beschrijving|
| --- | --- |
| --foutopsporing | Verhoog de logboekregistratie om alle foutopsporingslogboeken weer te geven. |
| --help -h | Dit helpbericht weergeven en afsluiten. |
| --output -o | Uitvoerindeling.  Toegestane waarden\: json, jsonc, tabel, tsv.  Standaard\: json. |
| --query | JMESPath-querytekenreeks. Zie\:http -jmespath.org/ voor meer informatie en voorbeelden. |
| --verbose | Verhoog de houtkap. Gebruik --debug voor volledige foutopsporingslogboeken. |

## <a name="sfctl-application-upload"></a>sfctl applicatie uploaden
Kopieer een servicefabric-toepassingspakket naar het afbeeldingsarchief.

Geef optioneel de voortgang van het uploaden weer voor elk bestand in het pakket. Voortgang uploaden `stderr`wordt verzonden naar .

### <a name="arguments"></a>Argumenten

|Argument|Beschrijving|
| --- | --- |
| --pad [Vereist] | Pad naar lokaal toepassingspakket. |
| --comprimeren | Alleen van toepassing op Service Fabric-toepassingspakketten. Maak een nieuwe map met het gecomprimeerde toepassingspakket naar de standaardlocatie of naar de locatie die is opgegeven door de parameter gecomprimeerdlocatie en upload de nieuw gemaakte map. <br><br> Als er al een gecomprimeerd bestand is gegenereerd door sfctl, wordt het overschreven als deze vlag is ingesteld. Er wordt een fout geretourneerd als de map geen toepassingspakket is. Als het al een gecomprimeerd toepassingspakket is, wordt de map gekopieerd zoals het is. Standaard wordt het nieuw gemaakte gecomprimeerde toepassingspakket verwijderd na een geslaagde upload. Als het uploaden niet lukt, moet u het gecomprimeerde pakket handmatig opruimen als dat nodig is. De verwijdering verwijdert geen lege dirs die mogelijk zijn gemaakt als de gecomprimeerde locatieparameter verwijst naar niet-bestaande mappen. |
| --gecomprimeerde locatie | De locatie om het gecomprimeerde toepassingspakket te plaatsen. <br><br> Als er geen locatie is opgegeven, wordt het gecomprimeerde pakket geplaatst onder een nieuw gemaakte map genaamd sfctl_compressed_temp onder de bovenliggende map die is opgegeven in het padargument. Als het padargument bijvoorbeeld waarde\:C /FolderA/AppPkg heeft, wordt het\:gecomprimeerde pakket toegevoegd aan C /FolderA/sfctl_compressed_temp/AppPkg. |
| --imagestore-tekenreeks | Bestemmingsafbeeldingsarchief om het toepassingspakket naar te uploaden.  \: Standaardfabric\:ImageStore. <br><br> Als u wilt uploaden naar een\:bestandslocatie, start u deze parameter met 'bestand'. Anders moet de waarde de verbindingstekenreeks voor het afbeeldingsarchief zijn, zoals de standaardwaarde. |
| --keep-compressed --keep-compressed --keep-compressed -- | Of het gegenereerde gecomprimeerde pakket moet worden behouden bij een succesvolle voltooiing van de upload. <br><br> Als dit niet is ingesteld, worden de gecomprimeerde app-pakketten verwijderd na een succesvolle voltooiing. Als het uploaden niet gelukt is, wordt het toepassingspakket altijd bewaard in de uitvoermap voor opnieuw uploaden. |
| --show-progress | Voortgang van het uploaden van bestanden weergeven voor grote pakketten. |
| --time-out -t | De totale time-out in seconden. Uploaden mislukt en retourfouten nadat de time-outduur van het uploaden is verstreken. Deze time-out is van toepassing op het volledige toepassingspakket en individuele time-outs voor bestanden zijn gelijk aan de resterende time-outduur. Time-out bevat niet de tijd die nodig is om het toepassingspakket te comprimeren.  Standaard\: 300. |

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
