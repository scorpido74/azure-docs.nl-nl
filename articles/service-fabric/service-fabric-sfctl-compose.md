---
title: Azure Service Fabric CLI- sfctl componeren
description: Meer informatie over sfctl, de opdrachtregelinterface van Azure Service Fabric. Bevat een lijst met opdrachten voor Docker Compose-toepassingen.
author: jeffj6123
ms.topic: reference
ms.date: 1/16/2020
ms.author: jejarry
ms.openlocfilehash: 1e40ca4e3c5ec8b7566646aa7ef723bd4c9e45a1
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "76906134"
---
# <a name="sfctl-compose"></a>sfctl compose
Docker Compose-toepassingen maken, verwijderen en beheren.

## <a name="commands"></a>Opdrachten

|Opdracht|Beschrijving|
| --- | --- |
| maken | Hiermee maakt u een implementatie van Service Fabric. |
| list | Hier wordt de lijst met samenstellende implementaties weergegeven die zijn gemaakt in het cluster Servicefabric. |
| verwijderen | Hiermee verwijdert u een bestaande servicestructuur voor het samenstellen van de implementatie uit het cluster. |
| status | Hier vindt u informatie over een implementatie van Service Fabric. |
| upgrade | Hiermee wordt gestart met het upgraden van een compositie-implementatie in het cluster Servicefabric. |
| upgrade-rollback | Hiermee wordt een upgrade voor een samenstellende implementatie in het cluster Servicefabric teruggedraaid. |
| upgradestatus | Krijgt details voor de nieuwste upgrade uitgevoerd op deze Service Fabric componeren implementatie. |

## <a name="sfctl-compose-create"></a>sfctl componeren maken
Hiermee maakt u een implementatie van Service Fabric.

### <a name="arguments"></a>Argumenten

|Argument|Beschrijving|
| --- | --- |
| --deployment-name [Vereist] | De naam van de implementatie. |
| --bestandspad [Vereist] | Pad naar het doeldocker-bestand. |
| --encrypted-pass | In plaats van te vragen om een containerregister wachtwoord, gebruik dan een reeds versleutelde pass-phrase. |
| --has-pass | Zal vragen om een wachtwoord voor het containerregister. |
| --time-out -t | Standaard\: 60. |
| --user | Gebruikersnaam om verbinding te maken met containerregister. |

### <a name="global-arguments"></a>Globale argumenten

|Argument|Beschrijving|
| --- | --- |
| --foutopsporing | Verhoog de logboekregistratie om alle foutopsporingslogboeken weer te geven. |
| --help -h | Dit helpbericht weergeven en afsluiten. |
| --output -o | Uitvoerindeling.  Toegestane waarden\: json, jsonc, tabel, tsv.  Standaard\: json. |
| --query | JMESPath-querytekenreeks. Zie\:http -jmespath.org/ voor meer informatie en voorbeelden. |
| --verbose | Verhoog de houtkap. Gebruik --debug voor volledige foutopsporingslogboeken. |

## <a name="sfctl-compose-list"></a>sfctl componeren lijst
Hier wordt de lijst met samenstellende implementaties weergegeven die zijn gemaakt in het cluster Servicefabric.

Hier wordt de status over de samenstellende implementaties die zijn gemaakt of in het proces van het maken in het cluster Servicefabric worden gemaakt. Het antwoord bevat de naam, status en andere details over de compositie-implementaties. Als de lijst met implementaties niet in een pagina past, wordt één pagina met resultaten geretourneerd, evenals een vervolgtoken, dat kan worden gebruikt om de volgende pagina te krijgen.

### <a name="arguments"></a>Argumenten

|Argument|Beschrijving|
| --- | --- |
| --continuation-token | De parameter vervolgtoken wordt gebruikt om volgende set resultaten te verkrijgen. Een vervolgtoken met een niet-lege waarde wordt opgenomen in de reactie van de API wanneer de resultaten van het systeem niet in één reactie passen. Wanneer deze waarde wordt doorgegeven aan de volgende API-aanroep, retourneert de API de volgende set resultaten. Als er geen verdere resultaten zijn, bevat het vervolgtoken geen waarde. De waarde van deze parameter mag niet worden gecodeerd met url's. |
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

## <a name="sfctl-compose-remove"></a>sfctl componeren verwijderen
Hiermee verwijdert u een bestaande servicestructuur voor het samenstellen van de implementatie uit het cluster.

Hiermee verwijdert u een bestaande implementatie van Service Fabric.

### <a name="arguments"></a>Argumenten

|Argument|Beschrijving|
| --- | --- |
| --deployment-name [Vereist] | De identiteit van de implementatie. |
| --time-out -t | De time-out van de server voor het uitvoeren van de bewerking in enkele seconden. Deze time-out geeft de tijdsduur aan die de client bereid is te wachten tot de gevraagde bewerking is voltooid. De standaardwaarde voor deze parameter is 60 seconden.  Standaard\: 60. |

### <a name="global-arguments"></a>Globale argumenten

|Argument|Beschrijving|
| --- | --- |
| --foutopsporing | Verhoog de logboekregistratie om alle foutopsporingslogboeken weer te geven. |
| --help -h | Dit helpbericht weergeven en afsluiten. |
| --output -o | Uitvoerindeling.  Toegestane waarden\: json, jsonc, tabel, tsv.  Standaard\: json. |
| --query | JMESPath-querytekenreeks. Zie\:http -jmespath.org/ voor meer informatie en voorbeelden. |
| --verbose | Verhoog de houtkap. Gebruik --debug voor volledige foutopsporingslogboeken. |

## <a name="sfctl-compose-status"></a>sfctl componeren status
Hier vindt u informatie over een implementatie van Service Fabric.

Geeft als resultaat de status van de compositie-implementatie die is gemaakt of die wordt gemaakt in het cluster Servicefabric en waarvan de naam overeenkomt met de implementatie die is opgegeven als de parameter. Het antwoord bevat de naam, status en andere details over de implementatie.

### <a name="arguments"></a>Argumenten

|Argument|Beschrijving|
| --- | --- |
| --deployment-name [Vereist] | De identiteit van de implementatie. |
| --time-out -t | De time-out van de server voor het uitvoeren van de bewerking in enkele seconden. Deze time-out geeft de tijdsduur aan die de client bereid is te wachten tot de gevraagde bewerking is voltooid. De standaardwaarde voor deze parameter is 60 seconden.  Standaard\: 60. |

### <a name="global-arguments"></a>Globale argumenten

|Argument|Beschrijving|
| --- | --- |
| --foutopsporing | Verhoog de logboekregistratie om alle foutopsporingslogboeken weer te geven. |
| --help -h | Dit helpbericht weergeven en afsluiten. |
| --output -o | Uitvoerindeling.  Toegestane waarden\: json, jsonc, tabel, tsv.  Standaard\: json. |
| --query | JMESPath-querytekenreeks. Zie\:http -jmespath.org/ voor meer informatie en voorbeelden. |
| --verbose | Verhoog de houtkap. Gebruik --debug voor volledige foutopsporingslogboeken. |

## <a name="sfctl-compose-upgrade"></a>sfctl componeren upgrade
Hiermee wordt gestart met het upgraden van een compositie-implementatie in het cluster Servicefabric.

Valideert de meegeleverde upgradeparameters en start het upgraden van de implementatie als de parameters geldig zijn.

### <a name="arguments"></a>Argumenten

|Argument|Beschrijving|
| --- | --- |
| --deployment-name [Vereist] | De naam van de implementatie. |
| --bestandspad [Vereist] | Pad naar het doel Docker componeren bestand. |
| --default-svc-type-health-map | JSON gecodeerd woordenboek dat het gezondheidsbeleid gebruikt om de gezondheid van de diensten te evalueren beschrijven. |
| --encrypted-pass | In plaats van te vragen om een containerregister wachtwoord, gebruik dan een reeds versleutelde pass-phrase. |
| --failure-action | Mogelijke waarden\: zijn 'Ongeldig', 'Rollback', 'Manual'. |
| --force-herstart | Processen worden met kracht opnieuw gestart tijdens de upgrade, zelfs als de codeversie niet is gewijzigd. <br><br> De upgrade wijzigt alleen de configuratie of gegevens. |
| --has-pass | Zal vragen om een wachtwoord voor het containerregister. |
| --health-check-retry | De tijdsduur tussen pogingen om statuscontroles uit te voeren als de toepassing of het cluster niet in orde is. |
| --health-check-stable | De hoeveelheid tijd dat de toepassing of het cluster gezond moet blijven voordat de upgrade doorgaat naar het volgende upgradedomein. <br><br> Het wordt eerst geïnterpreteerd als een tekenreeks die een ISO 8601-duur vertegenwoordigt. Als dat niet lukt, wordt het geïnterpreteerd als een getal dat het totale aantal milliseconden vertegenwoordigt. |
| --health-check-wait | De tijdsduur om te wachten na het voltooien van een upgradedomein voordat u het proces voor statuscontroles start. |
| --replica-set-check | De maximale tijd om de verwerking van een upgradedomein te blokkeren en verlies van beschikbaarheid te voorkomen wanneer er onverwachte problemen zijn. <br><br> Wanneer deze time-out verloopt, verloopt de verwerking van het upgradedomein ongeacht problemen met het verlies van beschikbaarheid. De time-out wordt aan het begin van elk upgradedomein opnieuw ingesteld. Geldige waarden liggen tussen 0 en 42949672925. |
| --svc-type-health-map | Json codeerde een lijst met objecten die de statusbeleid beschrijven die wordt gebruikt om de status van verschillende servicetypen te evalueren. |
| --time-out -t | Standaard\: 60. |
| --niet-healthy-app | Het maximaal toegestane percentage ongezonde toepassingen voordat een fout wordt gemeld. <br><br> Bijvoorbeeld, om 10% van de toepassingen ongezond te maken, zou deze waarde 10 zijn. Het percentage vertegenwoordigt het maximale getolereerde percentage toepassingen dat ongezond kan zijn voordat het cluster als fout wordt beschouwd. Als het percentage wordt gerespecteerd, maar er ten minste één ongezonde toepassing is, wordt de status geëvalueerd als Waarschuwing. Dit wordt berekend door het aantal ongezonde toepassingen te verdelen over het totale aantal toepassingsinstanties in het cluster. |
| --upgrade-domein-time-out | De hoeveelheid tijd die elk upgradedomein moet voltooien voordat FailureAction wordt uitgevoerd. <br><br> Het wordt eerst geïnterpreteerd als een tekenreeks die een ISO 8601-duur vertegenwoordigt. Als dat niet lukt, wordt het geïnterpreteerd als een getal dat het totale aantal milliseconden vertegenwoordigt. |
| --upgrade-soort | Standaard\: rollen. |
| --upgrade-modus | Mogelijke waarden\: zijn 'Invalid', 'UnmonitoredAuto', 'UnmonitoredManual', 'Monitored'.  Standaard\: Niet-gecontroleerdAuto. |
| --upgrade-time-out | De hoeveelheid tijd die de algehele upgrade moet voltooien voordat FailureAction wordt uitgevoerd. <br><br> Het wordt eerst geïnterpreteerd als een tekenreeks die een ISO 8601-duur vertegenwoordigt. Als dat niet lukt, wordt het geïnterpreteerd als een getal dat het totale aantal milliseconden vertegenwoordigt. |
| --user | Gebruikersnaam om verbinding te maken met containerregister. |
| --waarschuwing-als-fout | Geeft aan of waarschuwingen met dezelfde ernst worden behandeld als fouten. |

### <a name="global-arguments"></a>Globale argumenten

|Argument|Beschrijving|
| --- | --- |
| --foutopsporing | Verhoog de logboekregistratie om alle foutopsporingslogboeken weer te geven. |
| --help -h | Dit helpbericht weergeven en afsluiten. |
| --output -o | Uitvoerindeling.  Toegestane waarden\: json, jsonc, tabel, tsv.  Standaard\: json. |
| --query | JMESPath-querytekenreeks. Zie\:http -jmespath.org/ voor meer informatie en voorbeelden. |
| --verbose | Verhoog de houtkap. Gebruik --debug voor volledige foutopsporingslogboeken. |

## <a name="sfctl-compose-upgrade-rollback"></a>sfctl componeren upgrade-rollback
Hiermee wordt een upgrade voor een samenstellende implementatie in het cluster Servicefabric teruggedraaid.

Rollback een service fabric componeren implementatie upgrade.

### <a name="arguments"></a>Argumenten

|Argument|Beschrijving|
| --- | --- |
| --deployment-name [Vereist] | De identiteit van de implementatie. |
| --time-out -t | De time-out van de server voor het uitvoeren van de bewerking in enkele seconden. Deze time-out geeft de tijdsduur aan die de client bereid is te wachten tot de gevraagde bewerking is voltooid. De standaardwaarde voor deze parameter is 60 seconden.  Standaard\: 60. |

### <a name="global-arguments"></a>Globale argumenten

|Argument|Beschrijving|
| --- | --- |
| --foutopsporing | Verhoog de logboekregistratie om alle foutopsporingslogboeken weer te geven. |
| --help -h | Dit helpbericht weergeven en afsluiten. |
| --output -o | Uitvoerindeling.  Toegestane waarden\: json, jsonc, tabel, tsv.  Standaard\: json. |
| --query | JMESPath-querytekenreeks. Zie\:http -jmespath.org/ voor meer informatie en voorbeelden. |
| --verbose | Verhoog de houtkap. Gebruik --debug voor volledige foutopsporingslogboeken. |

## <a name="sfctl-compose-upgrade-status"></a>sfctl stel upgrade-status samen
Krijgt details voor de nieuwste upgrade uitgevoerd op deze Service Fabric componeren implementatie.

Geeft als resultaat de informatie over de status van de groep implementatie-upgrade samen met details om problemen met foutopsporing van toepassingsstatus te helpen.

### <a name="arguments"></a>Argumenten

|Argument|Beschrijving|
| --- | --- |
| --deployment-name [Vereist] | De identiteit van de implementatie. |
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