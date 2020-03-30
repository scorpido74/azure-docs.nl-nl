---
title: Begrijpen hoe de vrijwillige migratietool werkt voor Azure Monitor-waarschuwingen
description: Begrijp hoe het migratiehulpprogramma voor waarschuwingen werkt en los problemen op.
ms.topic: conceptual
ms.date: 07/10/2019
ms.author: yalavi
author: yalavi
ms.subservice: alerts
ms.openlocfilehash: 8cc77d13567910797cd519ac193b848f3ea434da
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79274812"
---
# <a name="understand-how-the-migration-tool-works"></a>Werking van het hulpprogramma voor migratie

Zoals [eerder aangekondigd](monitoring-classic-retirement.md), worden klassieke waarschuwingen in Azure Monitor op 31 augustus 2019 (was oorspronkelijk 30 juni 2019) in gebruik genomen. Een migratietool is beschikbaar in de Azure-portal voor klanten die klassieke waarschuwingsregels gebruiken en die zelf migratie willen activeren.

In dit artikel wordt uitgelegd hoe de vrijwillige migratietool werkt. Het beschrijft ook remedies voor een aantal veelvoorkomende problemen.

> [!NOTE]
> Vanwege vertraging bij de uitrol van migratietool is de pensioendatum voor klassieke waarschuwingen verlengd [tot 31 augustus 2019](https://azure.microsoft.com/updates/azure-monitor-classic-alerts-retirement-date-extended-to-august-31st-2019/) vanaf de oorspronkelijk aangekondigde datum van 30 juni 2019.

## <a name="classic-alert-rules-that-will-not-be-migrated"></a>Klassieke waarschuwingsregels die niet worden gemigreerd

> [!IMPORTANT]
> Waarschuwingen voor activiteitenlogboeken (inclusief waarschuwingen voor servicestatus) en logboekwaarschuwingen worden niet beïnvloed door de migratie. De migratie is alleen van toepassing op klassieke waarschuwingsregels [die hier](monitoring-classic-retirement.md#retirement-of-classic-monitoring-and-alerting-platform)worden beschreven.

Hoewel het hulpprogramma bijna alle [klassieke waarschuwingsregels](monitoring-classic-retirement.md#retirement-of-classic-monitoring-and-alerting-platform)kan migreren, zijn er enkele uitzonderingen. De volgende waarschuwingsregels worden niet gemigreerd met het hulpprogramma (of tijdens de automatische migratie vanaf september 2019):

- Klassieke waarschuwingsregels voor gaststatistieken van virtuele machines (zowel Windows als Linux). Zie de [richtlijnen voor het opnieuw maken van dergelijke waarschuwingsregels in nieuwe metrische waarschuwingen](#guest-metrics-on-virtual-machines) later in dit artikel.
- Klassieke waarschuwingsregels voor klassieke opslagstatistieken. Zie de [richtlijnen voor het bewaken van uw klassieke opslagaccounts.](https://azure.microsoft.com/blog/modernize-alerting-using-arm-storage-accounts/)
- Klassieke waarschuwingsregels voor sommige statistieken van opslagaccounten. Zie [details](#storage-account-metrics) later in dit artikel.
- Klassieke waarschuwingsregels voor sommige Cosmos DB-statistieken. Zie [details](#cosmos-db-metrics) later in dit artikel.
- Klassieke waarschuwingsregels voor alle klassieke virtuele machines en statistieken voor cloudservices (Microsoft.ClassicCompute/virtualMachines en Microsoft.ClassicCompute/domainNames/slots/rollen). Zie [details](#classic-compute-metrics) later in dit artikel.

Als uw abonnement dergelijke klassieke regels heeft, moet u deze handmatig migreren. Omdat we geen automatische migratie kunnen bieden, blijven bestaande, klassieke metrische waarschuwingen van deze typen werken tot juni 2020. Deze extensie geeft u de tijd om over te stappen op nieuwe waarschuwingen. U ook tot juni 2020 nieuwe klassieke waarschuwingen blijven maken op de hierboven vermelde uitzonderingen. Voor al het andere kunnen er na augustus 2019 echter geen nieuwe klassieke waarschuwingen worden gemaakt.

> [!NOTE]
> Naast de bovenstaande uitzonderingen, als uw klassieke waarschuwingsregels ongeldig zijn, d.w.z. ze zijn op [afgeschafte statistieken](#classic-alert-rules-on-deprecated-metrics) of bronnen die zijn verwijderd, worden ze niet gemigreerd en zijn ze niet beschikbaar nadat de service is uitgeschakeld.

### <a name="guest-metrics-on-virtual-machines"></a>Gaststatistieken op virtuele machines

Voordat u nieuwe metrische waarschuwingen maken voor gaststatistieken, moeten de gaststatistieken worden verzonden naar het aangepaste metrische archief azure monitor. Volg deze instructies om de Azure Monitor-sink in te schakelen in diagnostische instellingen:

- [Gaststatistieken inschakelen voor Windows VM's](collect-custom-metrics-guestos-resource-manager-vm.md)
- [Gaststatistieken inschakelen voor Linux VM's](collect-custom-metrics-linux-telegraf.md)

Nadat deze stappen zijn uitgevoerd, u nieuwe metrische waarschuwingen maken voor gaststatistieken. En nadat u nieuwe metrische waarschuwingen hebt gemaakt, u klassieke waarschuwingen verwijderen.

### <a name="storage-account-metrics"></a>Metrische gegevens voor opslagaccount

Alle klassieke waarschuwingen voor opslagaccounts kunnen worden gemigreerd, behalve waarschuwingen voor deze statistieken:

- PercentAuthorizationError PercentAuthorizationError
- PercentclientOtherError
- PercentNetworkError PercentNetworkError
- PercentserverOtherError
- ProcentSucces
- PercentisseringFout
- PercentTimeoutError PercentTimeoutError
- AnoniemeThrottlingError
- SASThrottlingError (SASThrottlingError)
- ThrottlingError

Klassieke waarschuwingsregels voor procentstatistieken moeten worden gemigreerd op basis [van de toewijzing tussen oude en nieuwe opslagstatistieken](https://docs.microsoft.com/azure/storage/common/storage-metrics-migration#metrics-mapping-between-old-metrics-and-new-metrics). Drempels moeten op de juiste manier worden gewijzigd, omdat de beschikbare nieuwe statistiek absoluut is.

Klassieke waarschuwingsregels op AnonymousThrottlingError, SASThrottlingError en ThrottlingError moeten worden opgesplitst in twee nieuwe waarschuwingen omdat er geen gecombineerde statistiek is die dezelfde functionaliteit biedt. De drempels moeten op passende wijze worden aangepast.

### <a name="cosmos-db-metrics"></a>Cosmos DB-metrische gegevens

Alle klassieke waarschuwingen op Cosmos DB-statistieken kunnen worden gemigreerd, behalve waarschuwingen over deze statistieken:

- Gemiddelde aanvragen per seconde
- Consistentieniveau
- Http 2xx
- Http 3xx
- Http 400
- Http 401
- Interne serverfout
- Max RUPM verbruikt per minuut
- Max.
- Mislukte aanvragen voor Mongo-telling
- Mislukte aanvragen voor Mongo-verwijderen
- Mislukte aanvragen voor Mongo-invoegen
- Mongo Andere mislukte aanvragen
- Kosten voor andere aanvragen van Mongo
- Mongo Andere aanvraag tarief
- Mislukte aanvragen voor Mongo-query
- Mislukte aanvragen voor Mongo-update
- Waargenomen leeslatentie
- Waargenomen schrijflatentie
- Beschikbaarheid van service
- Opslagcapaciteit
- Gewurgde aanvragen
- Totaal aantal aanvragen

Gemiddelde aanvragen per seconde, consistentieniveau, max RUPM verbruikt per minuut, Max RUUs per seconde, Waargenomen leeslatentie, waargenomen schrijflatentie, opslagcapaciteit zijn momenteel niet beschikbaar in het [nieuwe systeem](metrics-supported.md#microsoftdocumentdbdatabaseaccounts).

Waarschuwingen over aanvraagstatistieken zoals Http 2xx, Http 3xx, Http 400, Http 401, Interne serverfout, servicebeschikbaarheid, beperkte aanvragen en totaalaanvragen worden niet gemigreerd omdat de manier waarop aanvragen worden geteld, verschilt tussen klassieke statistieken en nieuwe statistieken. Waarschuwingen hierover moeten handmatig opnieuw worden gemaakt met drempels die zijn aangepast.

Waarschuwingen voor mongo-statistieken met mislukte aanvragen moeten worden opgesplitst in meerdere waarschuwingen, omdat er geen gecombineerde statistiek is die dezelfde functionaliteit biedt. De drempels moeten op passende wijze worden aangepast.

### <a name="classic-compute-metrics"></a>Klassieke rekenstatistieken

Waarschuwingen over klassieke compute metrics worden niet gemigreerd met de migratietool, omdat klassieke compute resources nog niet worden ondersteund met nieuwe waarschuwingen. Ondersteuning voor nieuwe waarschuwingen voor deze resourcetypen wordt in de toekomst toegevoegd. Zodra dat beschikbaar is, moeten klanten voor juni 2020 nieuwe gelijkwaardige waarschuwingsregels opnieuw maken op basis van hun klassieke waarschuwingsregels.

### <a name="classic-alert-rules-on-deprecated-metrics"></a>Klassieke waarschuwingsregels voor afgeschafte statistieken

Dit zijn klassieke waarschuwingsregels voor statistieken die eerder werden ondersteund, maar uiteindelijk werden afgeschaft. Een klein percentage van de klanten heeft mogelijk ongeldige klassieke waarschuwingsregels voor dergelijke statistieken. Aangezien deze waarschuwingsregels ongeldig zijn, worden ze niet gemigreerd.

| Resourcetype| Afgeschafte statistiek(en) |
|-------------|----------------- |
| Microsoft.DBforMySQL/servers | compute_consumption_percent, compute_limit |
| Microsoft.DBforPostgreSQL/servers | compute_consumption_percent, compute_limit |
| Microsoft.Network/publicIPAddresses | defaultddostriggerrate defaultddostriggerrate defaultddostriggerrate defaultd |
| Microsoft.SQL/servers/databases | service_level_objective, storage_limit, storage_used, throttling, dtu_consumption_percent, storage_used |
| Microsoft.Web/hostingOmgevingen/multirolepools | gemiddelde werkgeheugenset |
| Microsoft.Web/hostingOmgevingen/workerpools | ontvangen bytes, httpqueuelength |

## <a name="how-equivalent-new-alert-rules-and-action-groups-are-created"></a>Hoe gelijkwaardige nieuwe waarschuwingsregels en actiegroepen worden gemaakt

Het migratiehulpprogramma converteert uw klassieke waarschuwingsregels naar gelijkwaardige nieuwe waarschuwingsregels en actiegroepen. Voor de meeste klassieke waarschuwingsregels bevinden gelijkwaardige nieuwe waarschuwingsregels zich `windowSize` `aggregationType`op dezelfde statistiek met dezelfde eigenschappen zoals en . Er zijn echter enkele klassieke waarschuwingsregels voor statistieken die een andere, gelijkwaardige statistiek in het nieuwe systeem hebben. De volgende beginselen zijn van toepassing op de migratie van klassieke waarschuwingen, tenzij in het onderstaande gedeelte wordt vermeld:

- **Frequentie:** hiermee bepaalt u hoe vaak een klassieke of nieuwe waarschuwingsregel controleert op de voorwaarde. De `frequency` in klassieke waarschuwingsregels was niet configureerbaar door de gebruiker en was altijd 5 minuten voor alle resourcetypes behalve Application Insights componenten waarvoor het 1 min was. Dus frequentie van gelijkwaardige regels is ook ingesteld op 5 min en 1 min respectievelijk.
- **Aggregatietype:** hiermee definieert u hoe de statistiek wordt samengevoegd over het venster van belang. Het `aggregationType` is ook hetzelfde tussen klassieke waarschuwingen en nieuwe waarschuwingen voor de meeste statistieken. In sommige gevallen, omdat de statistiek verschilt tussen `aggregationType` klassieke `primary Aggregation Type` waarschuwingen en nieuwe waarschuwingen, wordt equivalent of de gedefinieerde voor de statistiek gebruikt.
- **Eenheden**: Eigenschap van de statistiek waarop waarschuwing wordt gemaakt. Sommige gelijkwaardige statistieken hebben verschillende eenheden. De drempel wordt zo nodig aangepast. Als de oorspronkelijke statistiek bijvoorbeeld seconden als eenheden heeft, maar de gelijkwaardige nieuwe statistiek milliSeconds als eenheden heeft, wordt de oorspronkelijke drempelwaarde vermenigvuldigd met 1000 om hetzelfde gedrag te garanderen.
- **Venstergrootte:** hiermee definieert u het venster waarover metrische gegevens worden samengevoegd om te vergelijken met de drempelwaarde. Voor `windowSize` standaardwaarden zoals 5 minuten, 15 minuten, 30 minuten, 1 uur, 3 uur, 6 uur, 12 uur, 1 dag, is er geen wijziging aangebracht voor een gelijkwaardige nieuwe waarschuwingsregel. Voor andere waarden `windowSize` wordt het dichtst gekozen om te worden gebruikt. Voor de meeste klanten heeft deze wijziging geen invloed. Voor een klein percentage van de klanten, kan er een noodzaak om de drempel te tweaken om exact hetzelfde gedrag te krijgen.

In de volgende secties beschrijven we de statistieken met een andere, gelijkwaardige statistiek in het nieuwe systeem. Metrische gegevens die hetzelfde blijven voor klassieke en nieuwe waarschuwingsregels wordt niet vermeld. U vindt [hier](metrics-supported.md)een lijst met statistieken die worden ondersteund in het nieuwe systeem.

### <a name="microsoftstorageaccountsservices"></a>Microsoft.StorageAccounts/services

Voor opslagaccountservices zoals blob, tabel, bestand en wachtrij worden de volgende statistieken toegewezen aan gelijkwaardige statistieken zoals hieronder weergegeven:

| Metrische in klassieke waarschuwingen | Gelijkwaardige statistiek in nieuwe waarschuwingen | Opmerkingen|
|--------------------------|---------------------------------|---------|
| AnoniemAutorisatieFout| Transactiestatistiek met afmetingen "ResponseType"="AuthorizationError" en "Authentication" = "Anoniem"| |
| AnoniemeclientOtherError | Transactiestatistiek met afmetingen "ResponseType"="ClientOtherError" en "Authenticatie" = "Anoniem" | |
| AnoniemeclientTimeOutFout| Transactiestatistiek met afmetingen "ResponseType"="ClientTimeOutError" en "Authenticatie" = "Anoniem" | |
| AnoniemNetwerkFout | Transactiestatistiek met afmetingen "ResponseType"="NetworkError" en "Authentication" = "Anoniem" | |
| AnoniemeServerOtherD | Transactiestatistiek met afmetingen "ResponseType"="ServerOtherError" en "Authenticatie" = "Anoniem" | |
| AnoniemeservertimeoutD | Transactiestatistiek met afmetingen "ResponseType"="ServerTimeOutError" en "Authenticatie" = "Anoniem" | |
| AnoniemSucces | Transactiestatistiek met afmetingen "ResponseType"="Succes" en "Authenticatie" = "Anoniem" | |
| AutorisatieFout | Transactiestatistiek met afmetingen "ResponseType"="AuthorizationError" | |
| AverageE2ELatency | SuccesE2ELatentie | |
| AverageServerLatency | SuccessServerLatentie | |
| Capaciteit | BlobCapaciteit | Gebruik `aggregationType` 'gemiddeld' in plaats van 'laatste'. Metric is alleen van toepassing op Blob-services |
| ClientOtherError | Transactiestatistiek met afmetingen "ResponseType"="ClientOtherError"  | |
| ClientTimeoutFout | Transactiestatistiek met afmetingen "ResponseType"="ClientTimeOutError" | |
| ContainerCount ContainerCount | ContainerCount ContainerCount | Gebruik `aggregationType` 'gemiddeld' in plaats van 'laatste'. Metric is alleen van toepassing op Blob-services |
| NetworkError | Transactiestatistiek met afmetingen "ResponseType"="NetworkError" | |
| Objectaantal | BlobCount| Gebruik `aggregationType` 'gemiddeld' in plaats van 'laatste'. Metric is alleen van toepassing op Blob-services |
| SASAuthorizationFout | Transactiestatistiek met afmetingen "ResponseType"="AuthorizationError" en "Authentication" = "SAS" | |
| SASClientOtherError | Transactiestatistiek met afmetingen "ResponseType"="ClientOtherError" en "Authenticatie" = "SAS" | |
| SASClientTimeOutDFout | Transactiestatistiek met afmetingen "ResponseType"="ClientTimeOutError" en "Authenticatie" = "SAS" | |
| SASNetworkError (SASNetworkError) | Transactiestatistiek met afmetingen "ResponseType"="NetworkError" en "Authentication" = "SAS" | |
| SASServerOtherD | Transactiestatistiek met afmetingen "ResponseType"="ServerOtherError" en "Authenticatie" = "SAS" | |
| SASServerTimeOutDFout | Transactiestatistiek met afmetingen "ResponseType"="ServerTimeOutError" en "Authenticatie" = "SAS" | |
| SASSucces | Transactiestatistiek met afmetingen "ResponseType"="Succes" en "Authenticatie" = "SAS" | |
| ServerOtherD | Transactiestatistiek met afmetingen "ResponseType"="ServerOtherError" | |
| ServerTimeOutDFout ServerTimeOutError ServerTimeOutD | Transactiestatistiek met afmetingen "ResponseType"="ServerTimeOutError"  | |
| Geslaagd | Transactiestatistiek met afmetingen "ResponseType"="Succes" | |
| TotalBillableRequests TotalBillableRequests TotalBillableRequests TotalBill| Transacties | |
| TotalEgress | Uitgaand verkeer | |
| TotalIngress | Inkomend verkeer | |
| TotalRequests | Transacties | |

### <a name="microsoftinsightscomponents"></a>Microsoft.insights/components

Voor Application Insights worden gelijkwaardige statistieken weergegeven zoals hieronder wordt weergegeven:

| Metrische in klassieke waarschuwingen | Gelijkwaardige statistiek in nieuwe waarschuwingen | Opmerkingen|
|--------------------------|---------------------------------|---------|
| availability.availabilityMetric.value | beschikbaarheidResultaten/beschikbaarheidPercentage|   |
| availability.durationMetric.value | beschikbaarheidResultaten/duur| Vermenigvuldig de oorspronkelijke drempelwaarde met 1000 als eenheden voor klassieke metrische zijn in seconden en voor nieuwe zijn in milliSeconds.  |
| basicExceptionBrowser.count basicExceptionBrowser.count basicExceptionBrowser.count basicException | uitzonderingen/browser|  Gebruik `aggregationType` 'tellen' in plaats van 'som'. |
| basicExceptionServer.count | uitzonderingen/server| Gebruik `aggregationType` 'tellen' in plaats van 'som'.  |
| clientPerformance.clientProces.value | browserTimings/verwerkingDuur| Vermenigvuldig de oorspronkelijke drempelwaarde met 1000 als eenheden voor klassieke metrische zijn in seconden en voor nieuwe zijn in milliSeconds.  |
| clientPerformance.networkConnection.value | browserTimings/netwerkDuur|  Vermenigvuldig de oorspronkelijke drempelwaarde met 1000 als eenheden voor klassieke metrische zijn in seconden en voor nieuwe zijn in milliSeconds. |
| clientPerformance.receiveRequest.value | browserTimings/receiveDuration| Vermenigvuldig de oorspronkelijke drempelwaarde met 1000 als eenheden voor klassieke metrische zijn in seconden en voor nieuwe zijn in milliSeconds.  |
| clientPerformance.sendRequest.value | browserTimings/sendDuration| Vermenigvuldig de oorspronkelijke drempelwaarde met 1000 als eenheden voor klassieke metrische zijn in seconden en voor nieuwe zijn in milliSeconds.  |
| clientPerformance.total.value | browserTimings/totalDuration| Vermenigvuldig de oorspronkelijke drempelwaarde met 1000 als eenheden voor klassieke metrische zijn in seconden en voor nieuwe zijn in milliSeconds.  |
| performanceCounter.available_bytes.value | performanceCounters/geheugenAvailableBytes|   |
| performanceCounter.io_data_bytes_per_sec.value | performanceCounters/processIOBytesPerSeconde|   |
| performanceCounter.number_of_exceps_thrown_per_sec.value | prestatietellers/exceptionsPerSeconde|   |
| performanceCounter.percentage_processor_time_normalized.value | performanceCounters/processCpuPercentage|   |
| performanceCounter.percentage_processor_time.value | performanceCounters/processCpuPercentage| Drempelwaarde moet op de juiste manier worden gewijzigd omdat de oorspronkelijke statistiek in alle kernen was en de nieuwe statistiek wordt genormaliseerd tot één kern. Migratietool wijzigt geen drempelwaarden.  |
| performanceCounter.percentage_processor_total.value | performanceCounters/processorCpuPercentage|   |
| performanceCounter.process_private_bytes.value | performanceCounters/processPrivateBytes|   |
| performanceCounter.request_execution_time.value | performanceCounters/requestExecutionTime|   |
| performanceCounter.requests_in_application_queue.value | performanceCounters/requestsInQueue|   |
| performanceCounter.requests_per_sec.value | prestatietellers/aanvragenPerSeconde|   |
| request.duur | verzoeken/duur| Vermenigvuldig de oorspronkelijke drempelwaarde met 1000 als eenheden voor klassieke metrische zijn in seconden en voor nieuwe zijn in milliSeconds.  |
| request.rate | verzoeken/tarief|   |
| requestFailed.count | aanvragen/mislukt| Gebruik `aggregationType` 'tellen' in plaats van 'som'.   |
| view.count | pageViews/count pageViews/count pageViews/count pageViews| Gebruik `aggregationType` 'tellen' in plaats van 'som'.   |

### <a name="microsoftdocumentdbdatabaseaccounts"></a>Microsoft.DocumentDB/databaseAccounts

Voor Cosmos DB zijn gelijkwaardige statistieken zoals hieronder weergegeven:

| Metrische in klassieke waarschuwingen | Gelijkwaardige statistiek in nieuwe waarschuwingen | Opmerkingen|
|--------------------------|---------------------------------|---------|
| Beschikbare opslagruimte     |Beschikbare opslagruimte|   |
| Gegevensgrootte | DataGebruik| |
| Documentaantal | Documenttelling||
| Indexgrootte | IndexGebruik||
| Mongo Telling Aanvraag Kosten| MongoRequestCharge met dimensie "CommandName" = "tellen"||
| Mongo-aanvraagpercentage | MongoRequestsTellen met dimensie "CommandName" = "tellen"||
| Mongo Verwijderaanvraagkosten | MongoRequestCharge met dimensie "CommandName" = "delete"||
| Aanvraagtarief voor mongo verwijderen | MongoRequestsTellen met dimensie "CommandName" = "verwijderen"||
| Aanvraagkosten invoegen mongo | MongoRequestCharge met dimensie "CommandName" = "invoegen"||
| Aanvraagpercentage voor invoegen van Mongo | MongoRequestsTellen met dimensie "CommandName" = "invoegen"||
| Kosten voor mongo-queryaanvragen | MongoRequestCharge met dimensie "CommandName" = "zoeken"||
| Mongo-aanvraagpercentage | MongoRequestsTellen met dimensie "CommandName" = "zoeken"||
| Mongo Update Aanvraag Kosten | MongoRequestCharge met dimensie "CommandName" = "update"||
| Service niet beschikbaar| Beschikbaarheid van service||
| TotalRequestUnits | TotalRequestUnits||

### <a name="how-equivalent-action-groups-are-created"></a>Hoe gelijkwaardige actiegroepen worden gemaakt

Klassieke waarschuwingsregels hadden e-mail-, webhook-, logische app- en runbook-acties die waren gekoppeld aan de waarschuwingsregel zelf. Nieuwe waarschuwingsregels maken gebruik van actiegroepen die kunnen worden hergebruikt voor meerdere waarschuwingsregels. Met het migratieprogramma wordt één actiegroep voor dezelfde acties gemaakt, ongeacht het aantal waarschuwingsregels dat de actie gebruikt. Actiegroepen die door het migratiegereedschap worden gemaakt, gebruiken de naamgevingsindeling 'Migrated_AG*'.

> [!NOTE]
> Klassieke waarschuwingen verzonden gelokaliseerde e-mails op basis van de landinplaats van de klassieke beheerder wanneer ze worden gebruikt om klassieke beheerdersrollen op de hoogte te stellen. Nieuwe waarschuwingse-mails worden verzonden via actiegroepen en zijn alleen in het Engels.

## <a name="rollout-phases"></a>Implementatiefasen

De migratietool wordt gefaseerd uitgerold naar klanten die klassieke waarschuwingsregels gebruiken. Eigenaren van abonnementen ontvangen een e-mail wanneer het abonnement klaar is om te worden gemigreerd met behulp van de tool.

> [!NOTE]
> Omdat de tool gefaseerd wordt uitgerold, ziet u mogelijk dat sommige van uw abonnementen nog niet klaar zijn om te worden gemigreerd tijdens de eerste fasen.

De meeste abonnementen zijn momenteel gemarkeerd als klaar voor migratie. Alleen abonnementen met klassieke waarschuwingen voor volgende resourcetypen zijn nog steeds niet klaar voor migratie.

- Microsoft.classicCompute/domainNames/slots/rollen
- Microsoft.insights/components

## <a name="who-can-trigger-the-migration"></a>Wie kan de migratie activeren?

Elke gebruiker die de ingebouwde rol van Monitoring Contributor op abonnementsniveau heeft, kan de migratie activeren. Gebruikers die een aangepaste rol hebben met de volgende machtigingen, kunnen ook de migratie activeren:

- */lezen
- Microsoft.Insights/actiegroepen/*
- Microsoft.Insights/Waarschuwingsregels/*
- Microsoft.Insights/metricAlerts/*
- Microsoft.AlertsManagement/smartDetectorAlertRules/*

> [!NOTE]
> Naast bovenstaande machtigingen moet uw abonnement bovendien worden geregistreerd bij microsoft.AlertsManagement-bronprovider. Dit is vereist om waarschuwingen voor mislukte afwijkingen te migreren op Application Insights. 

## <a name="common-problems-and-remedies"></a>Veelvoorkomende problemen en oplossingen

Nadat u [de migratie hebt geactiveerd,](alerts-using-migration-tool.md)ontvangt u e-mail op de adressen die u hebt opgegeven om u te laten weten dat de migratie is voltooid of als er actie van u nodig is. In dit gedeelte worden enkele veelvoorkomende problemen beschreven en hoe hiermee om te gaan.

### <a name="validation-failed"></a>Validatie is mislukt

Als gevolg van enkele recente wijzigingen in de klassieke waarschuwingsregels in uw abonnement, kan het abonnement niet worden gemigreerd. Dit probleem is tijdelijk. U de migratie opnieuw starten nadat de migratiestatus binnen enkele dagen klaar **voor migratie** is teruggezet.

### <a name="scope-lock-preventing-us-from-migrating-your-rules"></a>Scope lock die ons verhindert uw regels te migreren

Als onderdeel van de migratie worden nieuwe metrische waarschuwingen en nieuwe actiegroepen gemaakt en worden vervolgens klassieke waarschuwingsregels verwijderd. Een scopelock kan echter voorkomen dat we resources maken of verwijderen. Afhankelijk van het scopeslot kunnen sommige of alle regels niet worden gemigreerd. U dit probleem oplossen door de scopevergrendeling voor het abonnement, de resourcegroep of de resource, die wordt vermeld in het [migratiehulpprogramma,](https://portal.azure.com/#blade/Microsoft_Azure_Monitoring/MigrationBladeViewModel)te verwijderen en de migratie opnieuw te activeren. Scope lock kan niet worden uitgeschakeld en moet worden verwijderd voor de duur van het migratieproces. [Meer informatie over het beheren van scopesloten](../../azure-resource-manager/management/lock-resources.md#portal).

### <a name="policy-with-deny-effect-preventing-us-from-migrating-your-rules"></a>Beleid met 'Weigeren'-effect dat ons verhindert uw regels te migreren

Als onderdeel van de migratie worden nieuwe metrische waarschuwingen en nieuwe actiegroepen gemaakt en worden vervolgens klassieke waarschuwingsregels verwijderd. Een beleid kan ons echter beletten middelen te creëren. Afhankelijk van het beleid kunnen sommige of alle regels niet worden gemigreerd. Het beleid dat het proces blokkeert, wordt vermeld in het [migratiehulpprogramma](https://portal.azure.com/#blade/Microsoft_Azure_Monitoring/MigrationBladeViewModel). Los dit probleem op door:

- De abonnementen of resourcegroepen voor de duur van het migratieproces uitsluiten van de beleidstoewijzing. [Meer informatie over het uitsluitingsbereik voor beleid beheren](../../governance/policy/tutorials/create-and-manage.md#exempt-a-non-compliant-or-denied-resource-using-exclusion).
- Effect verwijderen of wijzigen naar 'audit' of 'toevoegen' (die bijvoorbeeld problemen met ontbrekende tags kunnen oplossen). [Meer informatie over het effect beleid beheren](../../governance/policy/concepts/definition-structure.md#policy-rule).

## <a name="next-steps"></a>Volgende stappen

- [Het hulpprogramma voor migratie gebruiken](alerts-using-migration-tool.md)
- [Voorbereiden op de migratie](alerts-prepare-migration.md)
