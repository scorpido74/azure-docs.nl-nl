---
title: Ondersteunde resourcetypen via Azure Resource Health | Microsoft Documenten
description: Ondersteunde resourcetypen via Azure Resource-status
ms.topic: conceptual
ms.date: 01/29/2019
ms.openlocfilehash: 3ba7b308c0c7671df8a652194830cb910cb89acc
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79258302"
---
# <a name="resource-types-and-health-checks-in-azure-resource-health"></a>Resourcetypen en statuscontroles in Azure-bronstatus
Hieronder vindt u een volledige lijst van alle controles die worden uitgevoerd via resourcestatus door resourcetypen.

## <a name="microsoftanalysisservicesservers"></a>Microsoft.AnalysisServices/servers
|Uitgevoerde controles|
|---|
|<ul><li>Is de server operationeel?</li><li>Heeft de server geen geheugen meer?</li><li>Start de server op?</li><li>Herstelt de server?</li></ul>|

## <a name="microsoftapimanagementservice"></a>Microsoft.ApiManagement/service
|Uitgevoerde controles|
|---|
|<ul><li>Is de Api Management-service operationeel?</li></ul>|

## <a name="microsoftbatchbatchaccounts"></a>Microsoft.Batch/batchAccounts
|Uitgevoerde controles|
|---|
|<ul><li>Is het Batch-account actief?</li><li>Is het poolquotum overschreden voor deze batchaccount?</li></ul>|

## <a name="microsoftcacheredis"></a>Microsoft.Cache/Redis
|Uitgevoerde controles|
|---|
|<ul><li>Zijn alle cacheknooppunten actief?</li><li>Kan de cache worden bereikt vanuit het datacenter?</li><li>Heeft de cache het maximum aantal verbindingen bereikt?</li><li> Heeft de cache het beschikbare geheugen uitgeput? </li><li>Heeft de cache te maken met een groot aantal paginafouten?</li><li>Staat de cache zwaar belast?</li></ul>|

## <a name="microsoftcdnprofile"></a>Microsoft.CDN/profiel
|Uitgevoerde controles|
|---|
|<ul> <li>Is de aanvullende portal toegankelijk voor CDN-configuratiebewerkingen?</li><li>Zijn er aanhoudende leveringsproblemen met de CDN-eindpunten?</li><li>Kunnen gebruikers de configuratie van hun CDN-bronnen wijzigen?</li><li>Worden configuratiewijzigingen in het verwachte tempo doorgevoerd?</li><li>Kunnen gebruikers de CDN-configuratie beheren met behulp van de Azure-portal, PowerShell of de API?</li> </ul>|

## <a name="microsoftclassiccomputevirtualmachines"></a>Microsoft.classiccompute/virtualmachines
|Uitgevoerde controles|
|---|
|<ul><li>Is de hostserver actief?</li><li>Is het opstarten van het host-besturingssysteem voltooid?</li><li>Is de virtuele machine container ingericht en ingeschakeld?</li><li>Is er netwerkconnectiviteit tussen de host en het opslagaccount?</li><li>Is het opstarten van het gastbesturingssysteem voltooid?</li><li>Is er doorlopend gepland onderhoud?</li><li>Is de host hardware gedegradeerd en voorspeld om binnenkort mislukken?</li></ul>|

## <a name="microsoftcognitiveservicesaccounts"></a>Microsoft.cognitiveservices/accounts
|Uitgevoerde controles|
|---|
|<ul><li>Kan het account worden bereikt vanuit het datacenter?</li><li>Is de Cognitive Services Resource Provider beschikbaar?</li><li>Is de cognitieve service beschikbaar in de juiste regio?</li><li>Kunnen leesbewerkingen worden uitgevoerd op het opslagaccount met de resourcemetagegevens?</li><li>Is het API-aanroepquotum bereikt?</li><li>Is de API-leeslimiet bereikt?</li></ul>|

## <a name="microsoftcomputevirtualmachines"></a>Microsoft.compute/virtualmachines
|Uitgevoerde controles|
|---|
|<ul><li>Is de server die deze virtuele machine host up and running?</li><li>Is het opstarten van het host-besturingssysteem voltooid?</li><li>Is de virtuele machine container ingericht en ingeschakeld?</li><li>Is er netwerkconnectiviteit tussen de host en het opslagaccount?</li><li>Is het opstarten van het gastbesturingssysteem voltooid?</li><li>Is er doorlopend gepland onderhoud?</li><li>Is de host hardware gedegradeerd en voorspeld om binnenkort mislukken?</li></ul>|

## <a name="microsoftdatafactoryfactories"></a>Microsoft.datafactory/fabrieken
|Uitgevoerde controles|
|---|
|<ul><li>Zijn er storingen bij de pijplijnuitgevoerd?</li><li>Is het cluster dat de Data Factory host gezond?</li></ul>|

## <a name="microsoftdatalakeanalyticsaccounts"></a>Microsoft.datalakeanalytics/accounts
|Uitgevoerde controles|
|---|
|<ul><li>Hebben gebruikers problemen ondervonden bij het indienen of aanbieden van hun Data Lake Analytics-vacatures?</li><li>Kunnen Data Lake Analytics-taken niet worden voltooid vanwege systeemfouten?</li></ul>|


## <a name="microsoftdatalakestoreaccounts"></a>Microsoft.datalakestore/accounts
|Uitgevoerde controles|
|---|
|<ul><li>Hebben gebruikers problemen ondervonden met het uploaden van gegevens naar Data Lake Store?</li><li>Hebben gebruikers problemen ondervonden bij het downloaden van gegevens uit de Data Lake Store?</li></ul>|

## <a name="microsoftdatamigrationservices"></a>Microsoft.datamigratie/services
|Uitgevoerde controles|
|---|
|<ul><li>Heeft de databasemigratieservice geen inlevering?</li><li>Is de databasemigratieservice gestopt vanwege inactiviteit of gebruikersverzoeken?</li></ul>|

## <a name="microsoftdatashareaccounts"></a>Microsoft.DataShare/accounts
|Uitgevoerde controles|
|---|
|<ul><li>Is het Data Share-account actief?</li><li>Is het cluster dat de gegevensshare host beschikbaar?</li></ul>|

## <a name="microsoftdbformariadbservers"></a>Microsoft.DBforMariaDB/servers
|Uitgevoerde controles|
|---|
|<ul><li>Is de server niet beschikbaar vanwege onderhoud?</li><li>Is de server niet beschikbaar vanwege een herconfiguratie?</li></ul>|

## <a name="microsoftdbformysqlservers"></a>Microsoft.DBforMySQL/servers
|Uitgevoerde controles|
|---|
|<ul><li>Is de server niet beschikbaar vanwege onderhoud?</li><li>Is de server niet beschikbaar vanwege een herconfiguratie?</li></ul>|

## <a name="microsoftdbforpostgresqlservers"></a>Microsoft.DBforPostgreSQL/servers
|Uitgevoerde controles|
|---|
|<ul><li>Is de server niet beschikbaar vanwege onderhoud?</li><li>Is de server niet beschikbaar vanwege een herconfiguratie?</li></ul>|

## <a name="microsoftdevicesiothubs"></a>Microsoft.devices/iothubs
|Uitgevoerde controles|
|---|
|<ul><li>Is de IoT-hub operationeel?</li></ul>|

## <a name="microsoftdocumentdbdatabaseaccounts"></a>Microsoft.documentdb/databaseAccounts
|Uitgevoerde controles|
|---|
|<ul><li>Zijn er database- of incassoaanvragen niet weergegeven vanwege een aazure cosmos DB-service die niet beschikbaar is?</li><li>Zijn er documentaanvragen niet weergegeven vanwege een aazure cosmos DB-service die niet beschikbaar is?</li></ul>|

## <a name="microsofteventhubnamespaces"></a>Microsoft.eventhub/naamruimten
|Uitgevoerde controles|
|---|
|<ul><li>Ervaart de naamruimte van de gebeurtenishubs door gebruikers gegenereerde fouten?</li><li>Wordt de naamruimte van de Gebeurtenishubs momenteel geüpgraded?</li></ul>|

## <a name="microsofthdinsightclusters"></a>Microsoft.hdinsight/clusters
|Uitgevoerde controles|
|---|
|<ul><li>Zijn kernservices beschikbaar op het HDInsight-cluster?</li><li>Heeft het HDInsight-cluster in rust toegang tot de sleutel voor BYOK-versleuteling?</li></ul>|

## <a name="microsoftkeyvaultvaults"></a>Microsoft.KeyVault/kluizen
|Uitgevoerde controles|
|---|
|<ul><li>Zijn aanvragen voor key vault mislukt als gevolg van azure KeyVault-platformproblemen?</li><li>Worden aanvragen om sleutelkluis te beperken vanwege te veel verzoeken van de klant?</li></ul>|

## <a name="microsoftmachinelearningwebservices"></a>Microsoft.MachineLearning/webServices
|Uitgevoerde controles|
|---|
|<ul><li>Is de webservice operationeel?</li></ul>|

## <a name="microsoftmediamediaservices"></a>Microsoft.Media/mediaservices
|Uitgevoerde controles|
|---|
|<ul><li>Is de mediadienst operationeel?</li></ul>|

## <a name="microsoftnetworkapplicationgateways"></a>Microsoft.network/applicationgateways
|Uitgevoerde controles|
|---|
|<ul><li>Worden de prestaties van de Application Gateway afgebroken?</li><li>Is de Application Gateway beschikbaar?</li></ul>|

## <a name="microsoftnetworkconnections"></a>Microsoft.network/connections
|Uitgevoerde controles|
|---|
|<ul><li>Is de VPN-tunnel verbonden?</li><li>Zijn er configuratieconflicten in de verbinding?</li><li>Zijn de vooraf gedeelde toetsen goed geconfigureerd?</li><li>Is het VPN on-premises apparaat bereikbaar?</li><li>Zijn er mismatches in het IPSec/IKE-beveiligingsbeleid?</li><li>Is de S2S VPN-verbinding goed ingericht of in een mislukte staat?</li><li>Is de VNET-naar-VNET-verbinding goed ingericht of in een mislukte staat?</li></ul>|

## <a name="microsoftnetworkexpressroutecircuits"></a>Microsoft.network/expressroutecircuits
|Uitgevoerde controles|
|---|
|<ul><li>Is het ExpressRoute circuit gezond?</li></ul>|

## <a name="microsoftnetworkfrontdoors"></a>Microsoft.network/frontdoors
|Uitgevoerde controles|
|---|
|<ul><li>Reageren backends van de voordeur met fouten op gezondheidssondes?</li><li>Zijn configuratiewijzigingen vertraagd?</li></ul>|

## <a name="microsoftnetworkloadbalancers"></a>Microsoft.network/LoadBalancers
|Uitgevoerde controles|
|---|
|<ul><li>Zijn de load balancing endpoints beschikbaar?</li></ul>|

## <a name="microsoftnetworkvirtualnetworkgateways"></a>Microsoft.network/virtualNetworkGateways
|Uitgevoerde controles|
|---|
|<ul><li>Is de VPN-gateway bereikbaar vanaf het internet?</li><li>Staat de VPN Gateway in stand-by modus?</li><li>Wordt de VPN-service uitgevoerd op de gateway?</li></ul>|

## <a name="microsoftnotificationhubsnamespace"></a>Microsoft.NotificationHubs/naamruimte
|Uitgevoerde controles|
|---|
|<ul><li>Kunnen runtime-bewerkingen zoals registratie, installatie of verzenden worden uitgevoerd op de naamruimte?</li></ul>|

## <a name="microsoftoperationalinsightsworkspaces"></a>Microsoft.operationalinsights/workspaces
|Uitgevoerde controles|
|---|
|<ul><li>Zijn er indexeringsvertragingen voor de werkruimte?</li></ul>|

## <a name="microsoftpowerbidedicatedcapacities"></a>Microsoft.PowerBIDedicated/Capaciteiten
|Uitgevoerde controles|
|---|
|<ul><li>Is de capaciteitsbron operationeel?</li><li>Zijn alle workloads actief?</li></ul>|

## <a name="microsoftpowerbiworkspacecollections"></a>Microsoft.PowerBI/werkruimteCollecties
|Uitgevoerde controles|
|---|
|<ul><li>Is het host-besturingssysteem up and running?</li><li>Is de workspaceCollection bereikbaar van buiten het datacenter?</li><li>Is de Power BI Resource Provider beschikbaar?</li><li>Is de Power BI-service beschikbaar in de juiste regio?</li></ul>|

## <a name="microsoftsearchsearchservices"></a>Microsoft.search/searchServices
|Uitgevoerde controles|
|---|
|<ul><li>Kunnen diagnostische bewerkingen worden uitgevoerd op het cluster?</li></ul>|

## <a name="microsoftservicebusnamespaces"></a>Microsoft.ServiceBus/naamruimten
|Uitgevoerde controles|
|---|
|<ul><li>Worden klanten door gebruikers gegenereerde Service Bus-fouten ervaren?</li><li>Ervaren gebruikers een toename van tijdelijke fouten als gevolg van een upgrade naar de naamruimte van servicebus?</li></ul>|

## <a name="microsoftservicefabricclusters"></a>Microsoft.ServiceFabric/clusters
|Uitgevoerde controles|
|---|
|<ul><li>Is het cluster Service Fabric operationeel?</li><li>Kan het cluster ServiceFabric worden beheerd via Azure Resource Manager?</li></ul>|

## <a name="microsoftsqlmanagedinstancesdatabases"></a>Microsoft.SQL/managedInstances/databases
|Uitgevoerde controles|
|---|
|<ul><li>Is de database operationeel?</li></ul>|

## <a name="microsoftsqlserverdatabases"></a>Microsoft.SQL/Server/databases
|Uitgevoerde controles|
|---|
|<ul><li>Zijn er aanmeldingen in de database?</li></ul>|

## <a name="microsoftstoragestorageaccounts"></a>Microsoft.Storage/storageAccounts
|Uitgevoerde controles|
|---|
|<ul><li>Zijn aanvragen om gegevens uit het opslagaccount te lezen mislukt als gevolg van problemen met het Azure Storage-platform?</li><li>Zijn aanvragen om gegevens naar het opslagaccount te schrijven mislukt als gevolg van problemen met het Azure Storage-platform?</li><li>Is het opslagcluster waar het opslagaccount zich bevindt niet beschikbaar?</li></ul>|

## <a name="microsoftstreamanalyticsstreamingjobs"></a>Microsoft.StreamAnalytics/streamingjobs
|Uitgevoerde controles|
|---|
|<ul><li>Zijn alle hosts waar de taak wordt uitgevoerd up and running?</li><li>Was de baan niet in staat om te beginnen?</li><li>Zijn er lopende runtime upgrades?</li><li>Is de taak in een verwachte status (bijvoorbeeld uitgevoerd of gestopt door de klant)?</li><li>Is de taak ondervonden uit het geheugen uitzonderingen?</li><li>Zijn er geplande compute-updates?</li><li>Is de Execution Manager (besturingsplan) beschikbaar?</li></ul>|

## <a name="microsoftwebserverfarms"></a>Microsoft.web/serverFarms
|Uitgevoerde controles|
|---|
|<ul><li>Is de hostserver actief?</li><li>Wordt internetinformatieservices uitgevoerd?</li><li>Wordt de Load balancer uitgevoerd?</li><li>Kan het App Service Plan worden bereikt vanuit het datacenter?</li><li>Is het opslagaccount dat de inhoud van de sites voor de serverFarm host?</li></ul>|

## <a name="microsoftwebsites"></a>Microsoft.web/sites
|Uitgevoerde controles|
|---|
|<ul><li>Is de hostserver actief?</li><li>Wordt de internetinformatieserver uitgevoerd?</li><li>Wordt de Load balancer uitgevoerd?</li><li>Is de Web App bereikbaar vanuit het datacenter?</li><li>Is het opslagaccount dat de site-inhoud host, beschikbaar?</li></ul>|

## <a name="next-steps"></a>Volgende stappen
-  Zie [Inleiding tot het Azure Service Health-dashboard](service-health-overview.md) en Inleiding tot Azure Resource [Health](resource-health-overview.md) om meer over deze services te weten te komen. 
-  [Veelgestelde vragen over Azure Resource Health](resource-health-faq.md)
- Stel waarschuwingen in zodat u op de hoogte wordt gesteld van gezondheidsproblemen. Zie [Waarschuwingen configureren voor servicestatusgebeurtenissen voor](../azure-monitor/platform/alerts-activity-log-service-notifications.md)meer informatie . 
