---
title: Inzicht in periodieke back-upconfiguratie
description: Gebruik de periodieke back-up- en herstelfunctie van Service Fabric voor het inschakelen van periodieke back-ups van uw toepassingsgegevens.
author: hrushib
ms.topic: article
ms.date: 2/01/2019
ms.author: hrushib
ms.openlocfilehash: 34c6495e094a1160f6ac75b9f098934d5cbce967
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75610145"
---
# <a name="understanding-periodic-backup-configuration-in-azure-service-fabric"></a>Inzicht in periodieke back-upconfiguratie in Azure Service Fabric

Het configureren van periodieke back-ups van uw betrouwbare stateful services of betrouwbare actoren bestaat uit de volgende stappen:

1. **Back-upbeleid maken:** In deze stap wordt een of meer back-upbeleid gemaakt, afhankelijk van de vereisten.

2. **Back-up inschakelen:** In deze stap koppelt u back-upbeleid dat in **stap 1 is** gemaakt, aan de vereiste entiteiten, _Toepassing,_ _Service_of een _partitie_.

## <a name="create-backup-policy"></a>Back-upbeleid maken

Een back-upbeleid bestaat uit de volgende configuraties:

* **Automatisch herstellen op gegevensverlies**: geeft aan of u herstel automatisch moet activeren met behulp van de laatst beschikbare back-up voor het geval de partitie een gebeurtenis voor gegevensverlies ondervindt.

* **Maximale incrementele back-ups:** hiermee definieert u het maximum aantal incrementele back-ups dat moet worden genomen tussen twee volledige back-ups. Max incrementele back-ups geven de bovengrens op. Een volledige back-up kan worden genomen voordat een opgegeven aantal incrementele back-ups worden voltooid in een van de volgende voorwaarden

    1. De replica heeft nog nooit een volledige back-up genomen omdat het primair is geworden.

    2. Sommige logboekrecords sinds de laatste back-up zijn afgekapt.

    3. Replica geslaagd voor de MaxAccumulatedBackupLogSizeInMB limiet.

* **Back-upschema:** de tijd of frequentie waarmee periodieke back-ups moeten worden gemaakt. Men kan plannen back-ups te worden terugkerende op bepaalde interval of op een vaste tijd dagelijks / wekelijks.

    1. **Op frequentie gebaseerde back-upplanning**: Dit type schema moet worden gebruikt als het nodig is om met vaste intervallen een back-up van gegevens te maken. Het gewenste tijdsinterval tussen twee opeenvolgende back-ups wordt gedefinieerd met de ISO8601-indeling. Het op frequentie gebaseerde back-upschema ondersteunt intervalresolutie tot op de minuut.
        ```json
        {
            "ScheduleKind": "FrequencyBased",
            "Interval": "PT10M"
        }
        ```

    2. **Op tijd gebaseerde back-upplanning:** dit type schema moet worden gebruikt als het nodig is om back-ups van gegevens te maken op specifieke tijdstippen van de dag of week. Het frequentietype schema kan dagelijks of wekelijks zijn.
        1. ** _Dagelijkse_ tijdgebaseerde back-upschema:** Dit type schema moet worden gebruikt als de noodzaak-id om back-up s te nemen op specifieke tijdstippen van de dag. Om dit op `ScheduleFrequencyType` te geven, stel je in op _Dagelijks_; en `RunTimes` ingesteld op een lijst van de gewenste tijd gedurende de dag in ISO8601-indeling, datum opgegeven samen met de tijd zal worden genegeerd. Bijvoorbeeld, `0001-01-01T18:00:00` vertegenwoordigt _6:00 PM_ elke dag, het negeren van datum deel _0001-01-01_. Hieronder voorbeeld illustreert de configuratie om dagelijkse back-up te activeren om _9:00 uur_ en _6:00 uur_ elke dag.

            ```json
            {
                "ScheduleKind": "TimeBased",
                "ScheduleFrequencyType": "Daily",
                "RunTimes": [
                  "0001-01-01T09:00:00Z",
                  "0001-01-01T18:00:00Z"
                ]
            }
            ```

        2. ** _Wekelijkse_ time-based back-upschema:** Dit type schema moet worden gebruikt als de noodzaak-id om gegevens back-up te nemen op specifieke tijdstippen van de dag. Om dit op `ScheduleFrequencyType` te geven, stel je in op _Wekelijks_; ingesteld `RunDays` op een lijst met dagen in een `RunTimes` week waarop back-up moet worden geactiveerd en ingesteld op een lijst met de gewenste tijd gedurende de dag in ISO8601-indeling, datum die samen met de tijd is opgegeven, wordt genegeerd. Lijst van dagen van een week wanneer de periodieke back-up te activeren. Hieronder voorbeeld illustreert de configuratie om dagelijkse back-up te activeren om _9:00 uur_ en _6:00 uur_ van maandag tot vrijdag.

            ```json
            {
                "ScheduleKind": "TimeBased",
                "ScheduleFrequencyType": "Weekly",
                "RunDays": [
                   "Monday",
                   "Tuesday",
                   "Wednesday",
                   "Thursday",
                   "Friday"
                ],
                "RunTimes": [
                  "0001-01-01T09:00:00Z",
                  "0001-01-01T18:00:00Z"
                ]
            }
            ```

* **Back-upopslag:** hiermee geeft u de locatie op om back-ups te uploaden. Opslag kan azure blob store of bestandsshare zijn.
    1. **Azure blob store**: Dit opslagtype moet worden geselecteerd wanneer het nodig is om gegenereerde back-ups op te slaan in Azure. Zowel _zelfstandige_ als _Azure-gebaseerde_ clusters kunnen dit opslagtype gebruiken. Beschrijving voor dit opslagtype vereist verbindingstekenreeks en naam van de container waar back-ups moeten worden geüpload. Als de container met de opgegeven naam niet beschikbaar is, wordt deze gemaakt tijdens het uploaden van een back-up.
        ```json
        {
            "StorageKind": "AzureBlobStore",
            "FriendlyName": "Azure_storagesample",
            "ConnectionString": "<Put your Azure blob store connection string here>",
            "ContainerName": "BackupContainer"
        }
        ```

    2. **Bestandsshare:** dit opslagtype moet worden geselecteerd voor _zelfstandige_ clusters wanneer het nodig is om on-premises back-ups van gegevens op te slaan. Beschrijving voor dit opslagtype vereist pad voor bestandsshare waar back-ups moeten worden geüpload. Toegang tot de bestandsshare kan worden geconfigureerd met een van de volgende opties
        1. _Geïntegreerde Windows-verificatie_, waarbij de toegang tot bestandsshare wordt verleend aan alle computers die behoren tot het cluster Service Fabric. Stel in dit geval volgende velden in om op bestandsshare gebaseerde _back-upopslag_ te configureren.

            ```json
            {
                "StorageKind": "FileShare",
                "FriendlyName": "Sample_FileShare",
                "Path": "\\\\StorageServer\\BackupStore"
            }
            ```

        2. _Het beveiligen van bestandsshare met gebruikersnaam en wachtwoord_, waarbij de toegang tot bestandsshare wordt verstrekt aan specifieke gebruikers. De opslagspecificatie voor bestandsshare biedt ook de mogelijkheid om secundaire gebruikersnaam en secundair wachtwoord op te geven om terugvalreferenties op te geven voor het geval verificatie mislukt met de primaire gebruikersnaam en het primaire wachtwoord. Stel in dit geval volgende velden in om op bestandsshare gebaseerde _back-upopslag_ te configureren.

            ```json
            {
                "StorageKind": "FileShare",
                "FriendlyName": "Sample_FileShare",
                "Path": "\\\\StorageServer\\BackupStore",
                "PrimaryUserName": "backupaccount",
                "PrimaryPassword": "<Password for backupaccount>",
                "SecondaryUserName": "backupaccount2",
                "SecondaryPassword": "<Password for backupaccount2>"
            }
            ```

> [!NOTE]
> Zorg ervoor dat de betrouwbaarheid van de opslag voldoet aan of hoger is dan de betrouwbaarheidsvereisten van back-upgegevens.
>

* **Bewaarbeleid:** hiermee geeft u het beleid op om back-ups in de geconfigureerde opslag te behouden. Alleen basisretentiebeleid wordt ondersteund.
    1. **Basisbewaarbeleid:** Met dit bewaarbeleid u een optimaal opslaggebruik garanderen door back-upbestanden te verwijderen die niet meer nodig zijn. `RetentionDuration`kan worden opgegeven om de tijdspanne in te stellen waarvoor back-ups in de opslag moeten worden bewaard. `MinimumNumberOfBackups`is een optionele parameter die kan worden opgegeven om ervoor te zorgen dat `RetentionDuration`het opgegeven aantal back-ups altijd behouden blijft, ongeacht de . Hieronder voorbeeld illustreert de configuratie om back-ups te behouden voor _10_ dagen en staat niet toe dat het aantal back-ups te gaan onder _de 20_.

        ```json
        {
            "RetentionPolicyType": "Basic",
            "RetentionDuration" : "P10D",
            "MinimumNumberOfBackups": 20
        }
        ```

## <a name="enable-periodic-backup"></a>Periodieke back-up inschakelen
Nadat het back-upbeleid is gedefinieerd om te voldoen aan de vereisten voor gegevensback-up, moet het back-upbeleid op de juiste manier worden gekoppeld aan een _toepassing_of _service_of een _partitie_.

### <a name="hierarchical-propagation-of-backup-policy"></a>Hiërarchische verspreiding van back-upbeleid
In Servicefabric is de relatie tussen toepassing, service en partities hiërarchisch, zoals uitgelegd in [toepassingsmodel](./service-fabric-application-model.md). Back-upbeleid kan worden gekoppeld aan een _toepassing,_ _service_of een _partitie_ in de hiërarchie. Back-upbeleid wordt hiërarchisch naar het volgende niveau gepropageerd. Ervan uitgaande dat er slechts één back-upbeleid is gemaakt en is gekoppeld aan een _toepassing,_ worden alle stateful partities die behoren tot alle _betrouwbare stateful services_ en _betrouwbare actoren_ van de _toepassing,_ ondersteund met behulp van het back-upbeleid. Of als het back-upbeleid is gekoppeld aan een _betrouwbare stateful-service,_ worden alle partities met behulp van het back-upbeleid ondersteund.

### <a name="overriding-backup-policy"></a>Dwingend back-upbeleid
Er kan een scenario zijn waarin back-ups met dezelfde back-upplanning vereist zijn voor alle services van de toepassing, behalve voor specifieke services waarbij gegevensback-up moet worden gemaakt met een hoger frequentieschema of een back-up moet maken naar een ander opslagaccount of fileshare. Als u dergelijke scenario's wilt aanpakken, biedt de back-upherstelservice de mogelijkheid om het gepropageerde beleid bij service en partitiebereik te overschrijven. Wanneer het back-upbeleid is gekoppeld aan _service_ of _partitie,_ wordt het gepropageerde back-upbeleid overschreven, indien van toepassing.

### <a name="example"></a>Voorbeeld

In dit voorbeeld wordt de installatie met twee toepassingen _MyApp_A_ en _MyApp_B gebruikt._ Toepassing _MyApp_A_ bevat twee betrouwbare stateful diensten, _SvcA1_ & _SvcA3_, en een Reliable Actor service, _ActorA2_. _SvcA1_ bevat drie partities, terwijl _ActorA2_ en _SvcA3_ elk twee partities bevatten.  Application _MyApp_B_ bevat drie Reliable Stateful services, _SvcB1_, _SvcB2_en _SvcB3_. _SvcB1_ en _SvcB2_ bevatten elk twee partities, terwijl _SvcB3_ drie partities bevat.

Ga ervan uit dat de vereisten voor gegevensback-up van deze toepassingen als volgt zijn

1. MyApp_A
    1. Maak dagelijks back-up smaken van gegevens voor alle partities van alle _betrouwbare stateful-services_ en _betrouwbare actoren_ die tot de toepassing behoren. Upload back-upgegevens naar locatie _BackupStore1_.

    2. Een van de diensten, _SvcA3,_ vereist elk uur een back-up van gegevens.

    3. Gegevensgrootte in partitie _SvcA1_P2_ is meer dan verwacht en de back-upgegevens moeten worden opgeslagen op verschillende opslaglocatie _BackupStore2_.

2. MyApp_B
    1. Maak elke zondag om 8:00 uur een back-up van gegevens voor alle partities van _de SvcB1-service._ Upload back-upgegevens naar locatie _BackupStore1_.

    2. Maak elke dag om 8:00 uur back-up van gegevens voor _partitieSvcB2_P1._ Upload back-upgegevens naar locatie _BackupStore1_.

Om aan deze vereisten voor back-ups voor gegevens te voldoen, worden back-upbeleid BP_1 BP_5 gemaakt en is back-up als volgt ingeschakeld.
1. MyApp_A
    1. Maak back-upbeleid, _BP_1,_ met op frequentie gebaseerdback-upschema waarbij de frequentie is ingesteld op 24 uur. en back-upopslag geconfigureerd voor het gebruik van opslaglocatie _BackupStore1_. Schakel dit beleid in voor application _MyApp_A_ met [Api voor toepassingsback-up inschakelen.](https://docs.microsoft.com/rest/api/servicefabric/sfclient-api-enableapplicationbackup) Met deze actie u back-ups maken met behulp van back-upbeleid _BP_1_ voor alle partities van _betrouwbare stateful-services_ en _betrouwbare actoren_ die behoren tot _MyApp_A._

    2. Maak back-upbeleid, _BP_2,_ met op frequentie gebaseerde back-upschema waarbij de frequentie is ingesteld op 1 uur. en back-upopslag geconfigureerd voor het gebruik van opslaglocatie _BackupStore1_. Schakel dit beleid in voor service _SvcA3_ met [behulp van Enable Service Backup](https://docs.microsoft.com/rest/api/servicefabric/sfclient-api-enableservicebackup) API. Met deze actie wordt het gepropageerde beleid _overschreven BP_1_ door expliciet back-upbeleid _BP_2_ voor alle partities van service _SvcA3_ die leiden tot back-up van gegevens met behulp van back-upbeleid _BP_2_ voor deze partities.

    3. Maak back-upbeleid, _BP_3,_ met op frequentie gebaseerde back-upschema waarbij de frequentie is ingesteld op 24 uur. en back-upopslag geconfigureerd voor het gebruik van opslaglocatie _BackupStore2_. Dit beleid inschakelen voor _partitieSvcA1_P2_ met [api voor partitionback-up inschakelen.](https://docs.microsoft.com/rest/api/servicefabric/sfclient-api-enablepartitionbackup) Met deze actie wordt het gepropageerde beleid _BP_1_ overschreven door expliciet back-upbeleid _BP_3_ voor _partitieSvcA1_P2_.

2. MyApp_B
    1. Maak back-upbeleid, _BP_4_, met tijdgebaseerde back-upschema waarbij het frequentietype schema is ingesteld op wekelijks, de looptijden zijn ingesteld op zondag en de looptijden zijn ingesteld op 8:00 uur. Back-upopslag die is geconfigureerd voor het gebruik van opslaglocatie _BackupStore1_. Schakel dit beleid in voor service _SvcB1_ met [behulp van Enable Service Backup](https://docs.microsoft.com/rest/api/servicefabric/sfclient-api-enableservicebackup) API. Met deze actie u back-ups van gegevens maken met behulp van back-upbeleid _BP_4_ voor alle partities van service _SvcB1_.

    2. Maak back-upbeleid, _BP_5,_ met een op tijd gebaseerd back-upschema waarbij het frequentietype schema is ingesteld op dagelijks en de looptijden zijn ingesteld op 8:00 uur. Back-upopslag die is geconfigureerd voor het gebruik van opslaglocatie _BackupStore1_. Schakel dit beleid in voor _SvcB2_P1_ partitie met behulp van Partition Backup API [inschakelen.](https://docs.microsoft.com/rest/api/servicefabric/sfclient-api-enablepartitionbackup) Met deze actie u back-ups van gegevens maken met _behulp_ van back-upbeleid BP_5 voor _SvcB2_P1._

Onderstaand diagram toont expliciet ingeschakeld back-upbeleid en gepropageerd back-upbeleid.

![Hiërarchie van servicefabric-toepassingen][0]

## <a name="disable-backup"></a>Back-up uitschakelen
Back-upbeleid kan worden uitgeschakeld wanneer er geen back-upgegevens nodig zijn. Back-upbeleid dat is ingeschakeld bij een _toepassing,_ kan alleen worden uitgeschakeld bij dezelfde _toepassing_ met [Disable Application Backup](https://docs.microsoft.com/rest/api/servicefabric/sfclient-api-disableapplicationbackup) API, Back-upbeleid ingeschakeld bij een _service_ kan bij dezelfde _service_ worden uitgeschakeld met Disable [Service Backup](https://docs.microsoft.com/rest/api/servicefabric/sfclient-api-disableservicebackup) API en Back-upbeleid ingeschakeld bij een _partitie_ kan worden uitgeschakeld bij dezelfde _partitie_ met behulp van Disable [Partition Backup](https://docs.microsoft.com/rest/api/servicefabric/sfclient-api-disablepartitionbackup) API.

* Als u het back-upbeleid voor een _toepassing_ uitschakelt, worden alle periodieke back-ups van gegevens gestopt als gevolg van de verspreiding van het back-upbeleid naar betrouwbare stateful-servicepartities of Reliable Actor-partities.

* Als u het back-upbeleid voor een _service_ uitschakelt, worden alle periodieke back-ups van gegevens gestopt als gevolg van de verspreiding van dit back-upbeleid naar de partities van de _service._

* Als u het back-upbeleid voor een _partitie_ uitschakelt, wordt alle periodieke back-ups van gegevens gestopt vanwege het back-upbeleid bij de partitie.

* Terwijl u een back-up uitschakelt voor `CleanBackup` een entiteit (toepassing/service/partitie), u instellen op _true_ om alle back-ups in geconfigureerde opslag te verwijderen.
    ```json
    {
        "CleanBackup": true 
    }
    ```

## <a name="suspend--resume-backup"></a>Back-up & hervatten
Bepaalde situatie kan een tijdelijke opschorting van de periodieke back-up van gegevens vereisen. In een dergelijke situatie kan, afhankelijk van de vereiste, de back-up-API worden gebruikt bij een _toepassing,_ _service_of _partitie_. Periodieke back-upopschorting is transitief over substructuur van de hiërarchie van de toepassing vanaf het punt dat wordt toegepast. 

* Wanneer opschorting wordt toegepast bij een _toepassing_ met behulp van [Suspend Application Backup](https://docs.microsoft.com/rest/api/servicefabric/sfclient-api-suspendapplicationbackup) API, worden alle services en partities onder deze toepassing opgeschort voor periodieke back-up van gegevens.

* Wanneer opschorting wordt toegepast bij een _service_ met [suspend Service Backup](https://docs.microsoft.com/rest/api/servicefabric/sfclient-api-suspendservicebackup) API, worden alle partities onder deze service opgeschort voor periodieke back-up van gegevens.

* Wanneer opschorting wordt toegepast op een _partitie_ met behulp van [Suspend Partition Backup](https://docs.microsoft.com/rest/api/servicefabric/sfclient-api-suspendpartitionbackup) API, worden de partities onder deze service opgeschort voor periodieke back-up van gegevens.

Zodra de noodzaak voor opschorting voorbij is, kan de periodieke gegevensback-up worden hersteld met behulp van de respectievelijke CV back-up API. Periodieke back-up moet worden hervat bij dezelfde _toepassing,_ _service_of _partitie_ waar deze is opgeschort.

* Als de opschorting is toegepast bij een _toepassing,_ moet deze worden hervat met [de Back-up-API voor toepassingen.](https://docs.microsoft.com/rest/api/servicefabric/sfclient-api-resumeapplicationbackup) 

* Als de opschorting is toegepast bij een _service,_ moet deze worden hervat met [de Back Service Backup](https://docs.microsoft.com/rest/api/servicefabric/sfclient-api-resumeservicebackup) API.

* Als de opschorting is toegepast op een _partitie,_ moet deze worden hervat met [de Back-partitieback-up-API.](https://docs.microsoft.com/rest/api/servicefabric/sfclient-api-resumepartitionbackup)

### <a name="difference-between-suspend-and-disable-backups"></a>Verschil tussen Back-ups onderbreken en uitschakelen
Back-up uitschakelen moet worden gebruikt wanneer back-ups niet langer nodig zijn voor een bepaalde toepassing, service of partitie. Men kan uitschakelen back-up aanvraag samen met schone back-ups parameter om waar te zijn, wat zou betekenen dat alle bestaande back-ups worden verwijderd ook. Echter, suspend moet worden gebruikt in scenario's waar men wil uitschakelen back-ups tijdelijk, zoals wanneer de lokale schijf wordt vol of het uploaden van back-up is mislukt als gevolg van bekende netwerk probleem, enz. 

Hoewel uitschakelen alleen kan worden ingeroepen op een niveau dat eerder is ingeschakeld voor back-up expliciet, kan opschorting worden toegepast op elk niveau dat momenteel is ingeschakeld voor back-up, hetzij rechtstreeks of via overerving / hiërarchie. Als back-up bijvoorbeeld is ingeschakeld op toepassingsniveau, kan men alleen op toepassingsniveau een beroep doen op uitschakelen, maar kan onderbreken worden aangeroepen bij toepassing, elke service of partitie onder die toepassing. 

## <a name="auto-restore-on-data-loss"></a>Automatisch herstellen op gegevensverlies
De servicepartitie kan gegevens verliezen als gevolg van onverwachte fouten. De schijf voor twee van de drie replica's voor een partitie (inclusief de primaire replica) wordt bijvoorbeeld beschadigd of gewist.

Wanneer Service Fabric detecteert dat de partitie gegevensverlies heeft, wordt de `OnDataLossAsync` interfacemethode op de partitie aanroepen en verwacht deze partitie de vereiste actie te ondernemen om uit gegevensverlies te komen. In deze situatie, als het effectieve `AutoRestoreOnDataLoss` back-upbeleid bij de partitie is ingesteld op `true` dan wordt het herstel automatisch geactiveerd met behulp van de laatst beschikbare back-up voor deze partitie.

## <a name="get-backup-configuration"></a>Back-upconfiguratie verzenden
Er worden afzonderlijke API's beschikbaar gesteld om back-upconfiguratiegegevens te krijgen bij een _toepassing,_ _service_en _partitiebereik._ [Informatie over configuratie van de back-up](https://docs.microsoft.com/rest/api/servicefabric/sfclient-api-getapplicationbackupconfigurationinfo)van toepassingen, [configuratiegegevens voor serviceback-ups](https://docs.microsoft.com/rest/api/servicefabric/sfclient-api-getservicebackupconfigurationinfo)en [configuratiegegevens voor partitieback-ups](https://docs.microsoft.com/rest/api/servicefabric/sfclient-api-getpartitionbackupconfigurationinfo) worden respectievelijk deze API's. Deze API's geven voornamelijk het toepasselijke back-upbeleid, het bereik waarmee het back-upbeleid wordt toegepast en back-upopschortingsdetails. Hieronder volgt een korte beschrijving over de geretourneerde resultaten van deze API's.

- Configuratie-informatie over de back-up van toepassingen: geeft de details van het back-upbeleid dat wordt toegepast bij de toepassing en alle overbereden beleidsregels bij services en partities die tot de toepassing behoren. Het bevat ook de opschortingsinformatie voor de toepassing en it-services en partities.

- Informatie over de configuratie van de serviceback-up: biedt de details van het effectieve back-upbeleid bij de service en de reikwijdte waarop dit beleid is toegepast en alle overbereden beleidsregels bij de partities. Het bevat ook de opschortingsinformatie voor de service en de partities.

- Configuratiegegevens voor partitieback-ups: geeft de details van het effectieve back-upbeleid bij de partitie en het bereik waarop dit beleid is toegepast. Het bevat ook de opschortingsinformatie voor de partities.

## <a name="list-available-backups"></a>Beschikbare back-ups weergeven

Beschikbare back-ups kunnen worden vermeld met api's voor back-ups. Het resultaat van API-aanroep bevat back-upgegevens met betrekking tot alle back-ups die beschikbaar zijn in de back-upopslag, die is geconfigureerd in het toepasselijke back-upbeleid. Er worden verschillende varianten van deze API aangeboden om beschikbare back-ups van een toepassing, service of partitie weer te geven. Deze API's ondersteunen het verkrijgen van de _laatst_ beschikbare back-up van alle toepasselijke partities of het filteren van back-ups op basis van _begin-_ en _einddatum._

Deze API's ondersteunen ook de resultaten, wanneer de parameter _MaxResults_ is ingesteld op een niet-nul positief geheel getal, retourneert de API maximale MaxResults-back-upinfoitems. _MaxResults_ In het geval dat er meer back-upinfo-items beschikbaar zijn dan de _Waarde MaxResults,_ wordt een vervolgtoken geretourneerd. Geldige parameter voor vervolgtoken kan worden gebruikt om de volgende set resultaten te krijgen. Wanneer geldige vervolgtokenwaarde wordt doorgegeven aan de volgende aanroep van de API, retourneert de API de volgende set resultaten. Er is geen vervolgtoken opgenomen in het antwoord wanneer alle beschikbare resultaten worden geretourneerd.

Hieronder volgt de korte informatie over ondersteunde varianten.

- [Lijst met toepassingsback-ups ophalen:](https://docs.microsoft.com/rest/api/servicefabric/sfclient-api-getapplicationbackuplist)retourneert een lijst met back-ups die beschikbaar zijn voor elke partitie die behoort tot de opgegeven Service Fabric-toepassing.

- [Serviceback-uplijst ophalen:](https://docs.microsoft.com/rest/api/servicefabric/sfclient-api-getservicebackuplist)retourneert een lijst met back-ups die beschikbaar zijn voor elke partitie die behoort tot de opgegeven Service Fabric-service.
 
- [Lijst met partitieback-ups weergeven:](https://docs.microsoft.com/rest/api/servicefabric/sfclient-api-getpartitionbackuplist)retourneert een lijst met back-ups die beschikbaar zijn voor de opgegeven partitie.

## <a name="next-steps"></a>Volgende stappen
- [Verwijzing naar REST API-back-up herstellen](https://docs.microsoft.com/rest/api/servicefabric/sfclient-index-backuprestore)

[0]: ./media/service-fabric-backuprestoreservice/backup-policy-association-example.png
