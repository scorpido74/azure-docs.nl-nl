---
title: Azure Service Fabric CLI- sfctl chaos
description: Meer informatie over sfctl, de opdrachtregelinterface van Azure Service Fabric. Bevat een lijst met opdrachten voor het beheren van chaos.
author: jeffj6123
ms.topic: reference
ms.date: 1/16/2020
ms.author: jejarry
ms.openlocfilehash: 6668446363361fbc6d24afc3d11a36a0b786667d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "76906160"
---
# <a name="sfctl-chaos"></a>sfctl chaos
Start, stop en rapporteer over de chaostestservice.

## <a name="subgroups"></a>Subgroepen
|Deelgroep|Beschrijving|
| --- | --- |
| [Schema](service-fabric-sfctl-chaos-schedule.md) | Pak het chaosschema. |
## <a name="commands"></a>Opdrachten

|Opdracht|Beschrijving|
| --- | --- |
| events | Hier wordt het volgende segment van de Gebeurtenissen voor Chaos op basis van het vervolgtoken of het tijdsbereik. |
| ophalen | Krijg de status van Chaos. |
| start | Start Chaos in het cluster. |
| stoppen | Stopt Chaos als deze in het cluster wordt uitgevoerd en zet het chaosschema in een gestopte status. |

## <a name="sfctl-chaos-events"></a>sfctl chaos evenementen
Hier wordt het volgende segment van de Gebeurtenissen voor Chaos op basis van het vervolgtoken of het tijdsbereik.

Als u het volgende segment van de gebeurtenissen Chaos wilt krijgen, u het Vervolgtoken opgeven. Als u het begin wilt krijgen van een nieuw segment van Chaos-gebeurtenissen, u het tijdsbereik opgeven via StartTimeUtc en EndTimeUtc. U niet zowel het Vervolgtoken als het tijdsbereik in hetzelfde gesprek opgeven. Wanneer er meer dan 100 Chaos-gebeurtenissen zijn, worden de gebeurtenissen chaos geretourneerd in meerdere segmenten waar een segment niet meer dan 100 Chaos-gebeurtenissen bevat en om het volgende segment te krijgen, belt u naar deze API met het vervolgtoken.

### <a name="arguments"></a>Argumenten

|Argument|Beschrijving|
| --- | --- |
| --continuation-token | De parameter vervolgtoken wordt gebruikt om volgende set resultaten te verkrijgen. Een vervolgtoken met een niet-lege waarde wordt opgenomen in de reactie van de API wanneer de resultaten van het systeem niet in één reactie passen. Wanneer deze waarde wordt doorgegeven aan de volgende API-aanroep, retourneert de API de volgende set resultaten. Als er geen verdere resultaten zijn, bevat het vervolgtoken geen waarde. De waarde van deze parameter mag niet worden gecodeerd met url's. |
| --end-time-utc | De Windows-bestandstijd die de eindtijd van het tijdsbereik weergeeft waarvoor een Chaos-rapport moet worden gegenereerd. Raadpleeg [de methode DateTime.ToFileTimeUtc](https\://msdn.microsoft.com/library/system.datetime.tofiletimeutc(v=vs.110).aspx) voor meer informatie. |
| --max-resultaten | Het maximum aantal resultaten dat moet worden geretourneerd als onderdeel van de paginaquery's. Deze parameter definieert de bovengrens op het aantal geretourneerde resultaten. De geretourneerde resultaten kunnen lager zijn dan de opgegeven maximale resultaten als ze niet in het bericht passen volgens de maximale beperkingen voor de grootte van het bericht die in de configuratie zijn gedefinieerd. Als deze parameter nul is of niet is opgegeven, bevat de paginaquery zoveel mogelijk resultaten die in het retourbericht passen. |
| --start-time-utc | De Windows-bestandstijd die de begintijd van het tijdsbereik weergeeft waarvoor een Chaos-rapport moet worden gegenereerd. Raadpleeg [de methode DateTime.ToFileTimeUtc](https\://msdn.microsoft.com/library/system.datetime.tofiletimeutc(v=vs.110).aspx) voor meer informatie. |
| --time-out -t | De time-out van de server voor het uitvoeren van de bewerking in enkele seconden. Deze time-out geeft de tijdsduur aan die de client bereid is te wachten tot de gevraagde bewerking is voltooid. De standaardwaarde voor deze parameter is 60 seconden.  Standaard\: 60. |

### <a name="global-arguments"></a>Globale argumenten

|Argument|Beschrijving|
| --- | --- |
| --foutopsporing | Verhoog de logboekregistratie om alle foutopsporingslogboeken weer te geven. |
| --help -h | Dit helpbericht weergeven en afsluiten. |
| --output -o | Uitvoerindeling.  Toegestane waarden\: json, jsonc, tabel, tsv.  Standaard\: json. |
| --query | JMESPath-querytekenreeks. Zie\:http -jmespath.org/ voor meer informatie en voorbeelden. |
| --verbose | Verhoog de houtkap. Gebruik --debug voor volledige foutopsporingslogboeken. |

## <a name="sfctl-chaos-get"></a>sfctl chaos te krijgen
Krijg de status van Chaos.

Krijg de status van Chaos die aangeeft of Chaos wordt uitgevoerd, de Chaos-parameters die worden gebruikt voor het uitvoeren van Chaos en de status van het Chaos-schema.

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

## <a name="sfctl-chaos-start"></a>sfctl chaos start
Start Chaos in het cluster.

Als Chaos nog niet in het cluster wordt uitgevoerd, begint chaos met de doorgegeven chaosparameters. Als Chaos al wordt uitgevoerd wanneer deze oproep wordt uitgevoerd, mislukt de oproep met de foutcode FABRIC_E_CHAOS_ALREADY_RUNNING. Raadpleeg het artikel [Induce controlled Chaos in Service Fabric clusters](https\://docs.microsoft.com/azure/service-fabric/service-fabric-controlled-chaos) voor meer details.

### <a name="arguments"></a>Argumenten

|Argument|Beschrijving|
| --- | --- |
| --app-type-health-policy-map | JSON gecodeerde lijst met maximaal percentage ongezonde toepassingen voor specifieke toepassingstypen. Elk item geeft als sleutel de naam van het toepassingstype op en als een waarde die het percentage MaxPercentUnhealthyApplications vertegenwoordigt dat wordt gebruikt om de toepassingen van het opgegeven toepassingstype te evalueren. <br><br> Hiermee definieert u een kaart met maximaal percentage ongezonde toepassingen voor specifieke toepassingstypen. Elk item geeft als sleutel de naam van het toepassingstype en als waarde een geheel getal op dat het percentage MaxPercentUnhealthyApplications vertegenwoordigt dat wordt gebruikt om de toepassingen van het opgegeven toepassingstype te evalueren. De beleidskaart voor statusbeleid van het toepassingstype kan worden gebruikt tijdens de evaluatie van de clusterstatus om speciale toepassingstypen te beschrijven. De toepassingstypen in de kaart worden beoordeeld op basis van het percentage dat in de kaart is opgegeven en niet met de algemene MaxPercentUnhealthyApplications die zijn gedefinieerd in het clusterstatusbeleid. De toepassingen van toepassingstypen die in de kaart zijn opgegeven, worden niet meegeteld bij de wereldwijde groep toepassingen. Als sommige toepassingen van een type bijvoorbeeld kritiek zijn, kan de clusterbeheerder een vermelding aan de kaart toevoegen voor dat toepassingstype en deze een waarde van 0% toewijzen (dat wil zeggen geen fouten tolereren). Alle andere toepassingen kunnen worden geëvalueerd met MaxPercentUnhealthyApplications ingesteld op 20% om een aantal fouten uit de duizenden toepassingsinstanties te tolereren. De beleidskaart voor statusbeleid van het toepassingstype wordt alleen gebruikt als het clustermanifest de statusevaluatie van het toepassingstype mogelijk maakt met behulp van de configuratievermelding voor HealthManager/EnableApplicationTypeHealthEvaluation. |
| --chaos-target-filter | JSON gecodeerd woordenboek met twee string type toetsen. De twee toetsen zijn NodeTypeInclusionList en ApplicationInclusionList. Waarden voor beide toetsen zijn een lijst met tekenreeksen. chaos_target_filter definieert alle filters voor gerichte Chaos fouten, bijvoorbeeld, fouten alleen bepaalde knooppunt typen of fouten alleen bepaalde toepassingen. <br><br> Als chaos_target_filter niet wordt gebruikt, maakt Chaos fouten in alle clusterentiteiten. Als chaos_target_filter wordt gebruikt, maakt Chaos alleen fouten bij de entiteiten die voldoen aan de chaos_target_filter-specificatie. NodeTypeInclusionList en ApplicationInclusionList staan alleen een union semantics toe. Het is niet mogelijk om een kruising van NodeTypeInclusionList en ApplicationInclusionList op te geven. Het is bijvoorbeeld niet mogelijk om 'fout deze toepassing alleen op te geven wanneer deze op dat knooppunttype staat'. Zodra een entiteit is opgenomen in Een NodeTypeInclusionList of ApplicationInclusionList, kan die entiteit niet worden uitgesloten met ChaosTargetFilter. Zelfs als applicationX niet wordt weergegeven in ApplicationInclusionList, kan in sommige Chaos iteratie applicationX worden verweten omdat het toevallig op een knooppunt van nodeTypeY staat dat is opgenomen in NodeTypeInclusionList. Als zowel NodeTypeInclusionList als ApplicationInclusionList leeg zijn, wordt een ArgumentException gegooid. Alle soorten fouten (knooppunt opnieuw opstarten, codepakket opnieuw opstarten, replica verwijderen, replica opnieuw opstarten, primaire verplaatsen en secundair verplaatsen) zijn ingeschakeld voor de knooppunten van deze knooppunttypen. Als een knooppunttype (bijvoorbeeld NodeTypeX) niet wordt weergegeven in de NodeTypeInclusionList, worden nodeniveaufouten (zoals NodeRestart) nooit ingeschakeld voor de knooppunten van NodeTypeX, maar codepakket- en replicafouten kunnen nog steeds worden ingeschakeld voor NodeTypeX als een toepassing in de ApplicationInclusionList bevindt zich toevallig op een knooppunt van NodeTypeX. Maximaal 100 node type namen kunnen worden opgenomen in deze lijst, om dit aantal te verhogen, een config upgrade is vereist voor MaxNumberNumberNumberNumberNumberDeTypesInChaosEntityFilter configuratie. Alle replica's die behoren tot services van deze toepassingen zijn vatbaar voor replicafouten (replica opnieuw opstarten, replica verwijderen, primaire verplaatsen en secundaire verplaatsen) door Chaos. Chaos kan een codepakket alleen opnieuw starten als het codepakket alleen replica's van deze toepassingen host. Als een toepassing niet in deze lijst wordt weergegeven, kan deze nog steeds worden verweten in een chaos-iteratie als de toepassing op een knooppunt van een knooppunttype terechtkomt dat is opgenomen in NodeTypeInclusionList. Als applicationX echter is gekoppeld aan nodeTypeY door plaatsingsbeperkingen en applicationX afwezig is in ApplicationInclusionList en nodeTypeY afwezig is in NodeTypeInclusionList, zal applicationX nooit worden verweten. Maximaal 1000 toepassingsnamen kunnen in deze lijst worden opgenomen, om dit aantal te verhogen, is een config-upgrade vereist voor de configuratie van MaxNumberNumberApplicationsInChaosEntityFilter. |
| --context | JSON gecodeerde kaart van (string, string) type sleutel-waarde paren. De kaart kan worden gebruikt om informatie over de Chaos run op te nemen. Er kunnen niet meer dan 100 van dergelijke paren en elke string (toets of waarde) kan ten hoogste 4095 tekens lang. Deze kaart wordt ingesteld door de starter van de Chaos-run om optioneel de context over de specifieke run op te slaan. |
| --disable-move-replica-fouten | Hiermee schakelt u de primaire beweging uit en verplaatst u secundaire fouten. |
| --max-cluster-stabilisatie | De maximale tijd om te wachten tot alle clusterentiteiten stabiel en gezond zijn geworden.  Standaard\: 60. <br><br> Chaos wordt uitgevoerd in iteraties en aan het begin van elke iteratie valideert het de status van clusterentiteiten. Tijdens de validatie als een clusterentiteit niet stabiel en gezond is binnen MaxClusterStabilizationTimeoutInSeconds, genereert Chaos een validatiemislukte gebeurtenis. |
| --max-gelijktijdige-fouten | Het maximum aantal gelijktijdige fouten per iteratie. Chaos wordt uitgevoerd in iteraties en twee opeenvolgende iteraties worden gescheiden door een validatiefase. Hoe hoger de gelijktijdigheid, hoe agressiever de injectie van fouten - het induceren van meer complexe reeksen staten om bugs te ontdekken. De aanbeveling is om te beginnen met een waarde van 2 of 3 en om voorzichtig te zijn tijdens het verplaatsen omhoog.  Standaard\: 1. |
| --max-procent-ongezonde-apps | Bij het evalueren van de clusterstatus tijdens Chaos wordt het maximaal toegestane percentage ongezonde toepassingen weergegeven voordat een fout wordt gemeld. <br><br> Het maximaal toegestane percentage ongezonde toepassingen voordat een fout wordt gemeld. Bijvoorbeeld, om 10% van de toepassingen ongezond te maken, zou deze waarde 10 zijn. Het percentage vertegenwoordigt het maximale getolereerde percentage toepassingen dat ongezond kan zijn voordat het cluster als fout wordt beschouwd. Als het percentage wordt gerespecteerd, maar er ten minste één ongezonde toepassing is, wordt de status geëvalueerd als Waarschuwing. Dit wordt berekend door het aantal ongezonde toepassingen te verdelen over het totale aantal toepassingsexemplaren in het cluster, met uitzondering van toepassingen van toepassingstypen die zijn opgenomen in de ApplicationTypeHealthPolicyMap. De berekening rondt af om één fout op kleine aantallen toepassingen te tolereren. Standaardpercentage is nul. |
| --max-procent-ongezonde knooppunten | Bij het evalueren van de clusterstatus tijdens Chaos wordt het maximaal toegestane percentage ongezonde knooppunten weergegeven voordat een fout wordt gemeld. <br><br> Het maximaal toegestane percentage ongezonde knooppunten voordat een fout wordt gemeld. Als u bijvoorbeeld wilt toestaan dat 10% van de knooppunten ongezond is, is deze waarde 10. Het percentage vertegenwoordigt het maximale getolereerde percentage knooppunten dat ongezond kan zijn voordat het cluster als fout wordt beschouwd. Als het percentage wordt gerespecteerd, maar er ten minste één ongezond knooppunt is, wordt de status geëvalueerd als Waarschuwing. Het percentage wordt berekend door het aantal ongezonde knooppunten te verdelen over het totale aantal knooppunten in het cluster. De berekening wordt afgerond om één fout op kleine aantallen knooppunten te tolereren. Standaardpercentage is nul. In grote clusters zijn sommige knooppunten altijd down of out voor reparaties, dus dit percentage moet worden geconfigureerd om dat te tolereren. |
| --time-to-run | Totale tijd (in seconden) waarvoor Chaos wordt uitgevoerd voordat deze automatisch stopt. De maximaal toegestane waarde is 4.294.967.295 (System.UInt32.MaxValue).  Standaard\: 4294967295. |
| --time-out -t | Standaard\: 60. |
| --wachttijd-tussen-fouten | Wachttijd (in seconden) tussen opeenvolgende fouten binnen één iteratie.  Standaard\: 20. <br><br> Hoe groter de waarde, hoe lager de overlapping tussen fouten en hoe eenvoudiger de volgorde van de statusovergangen die het cluster doormaakt. De aanbeveling is om te beginnen met een waarde tussen 1 en 5 en voorzichtig te zijn bij het omhoog gaan. |
| --wait-time-between-iteraties | Tijdscheiding (in seconden) tussen twee opeenvolgende iteraties van Chaos. Hoe groter de waarde, hoe lager de foutinjectiesnelheid.  Standaard\: 30. |
| --waarschuwing-als-fout | Geeft aan of waarschuwingen met dezelfde ernst worden behandeld als fouten. |

### <a name="global-arguments"></a>Globale argumenten

|Argument|Beschrijving|
| --- | --- |
| --foutopsporing | Verhoog de logboekregistratie om alle foutopsporingslogboeken weer te geven. |
| --help -h | Dit helpbericht weergeven en afsluiten. |
| --output -o | Uitvoerindeling.  Toegestane waarden\: json, jsonc, tabel, tsv.  Standaard\: json. |
| --query | JMESPath-querytekenreeks. Zie\:http -jmespath.org/ voor meer informatie en voorbeelden. |
| --verbose | Verhoog de houtkap. Gebruik --debug voor volledige foutopsporingslogboeken. |

## <a name="sfctl-chaos-stop"></a>sfctl chaos stoppen
Stopt Chaos als deze in het cluster wordt uitgevoerd en zet het chaosschema in een gestopte status.

Voorkomt dat Chaos nieuwe fouten uitvoert. In-flight fouten zullen blijven uitvoeren totdat ze zijn voltooid. Het huidige chaosschema wordt in een gestopte status geplaatst. Zodra een schema is gestopt, blijft het in de gestopte staat en wordt het niet gebruikt om nieuwe runs van Chaos te chaosplannen. Er moet een nieuw chaosschema worden ingesteld om de planning te hervatten.

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
- [Stel](service-fabric-cli.md) de SERVICE Fabric CLI in.
- Meer informatie over het gebruik van de CLI van de ServiceFabric met behulp van de [voorbeeldscripts](/azure/service-fabric/scripts/sfctl-upgrade-application).