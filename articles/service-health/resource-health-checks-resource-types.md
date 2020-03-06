---
title: Ondersteunde resource typen via Azure Resource Health | Microsoft Docs
description: Ondersteunde resource typen via Azure resource Health
ms.topic: conceptual
ms.date: 01/29/2019
ms.openlocfilehash: f5d1c720d3cbafafc81276a968abf2003bbd46ce
ms.sourcegitcommit: 509b39e73b5cbf670c8d231b4af1e6cfafa82e5a
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/05/2020
ms.locfileid: "78373451"
---
# <a name="resource-types-and-health-checks-in-azure-resource-health"></a>Resource typen en status controles in azure resource Health
Hieronder vindt u een volledige lijst met alle controles die worden uitgevoerd door middel van resource typen.

## <a name="microsoftanalysisservicesservers"></a>Microsoft.AnalysisServices/servers
|Controles uitgevoerd|
|---|
|<ul><li>Is de server actief?</li><li>Is er onvoldoende geheugen beschikbaar voor de server?</li><li>Wordt de server opgestart?</li><li>Wordt de server hersteld?</li></ul>|

## <a name="microsoftapimanagementservice"></a>Microsoft.ApiManagement/service
|Controles uitgevoerd|
|---|
|<ul><li>Is de API Management-service actief?</li></ul>|

## <a name="microsoftbatchbatchaccounts"></a>Microsoft.Batch/batchAccounts
|Controles uitgevoerd|
|---|
|<ul><li>Wordt het batch-account actief?</li><li>Is het groeps quotum overschreden voor dit batch-account?</li></ul>|

## <a name="microsoftcacheredis"></a>Microsoft.Cache/Redis
|Controles uitgevoerd|
|---|
|<ul><li>Zijn alle cache knooppunten actief?</li><li>Kan de cache worden bereikt binnen het Data Center?</li><li>Is het maximum aantal verbindingen voor de cache bereikt?</li><li> Heeft de cache het beschik bare geheugen uitgeput? </li><li>Is er sprake van een groot aantal pagina fouten in de cache?</li><li>Is de cache onder zware belasting?</li></ul>|

## <a name="microsoftcdnprofile"></a>Microsoft.CDN/profile
|Controles uitgevoerd|
|---|
|<ul> <li>Is de aanvullende portal toegankelijk voor CDN-configuratie bewerkingen?</li><li>Zijn er doorlopende leverings problemen met de CDN-eind punten?</li><li>Kunnen gebruikers de configuratie van de CDN-bronnen wijzigen?</li><li>Worden er wijzigingen in de configuratie doorgevoerd tegen het verwachte aantal?</li><li>Kunnen gebruikers de CDN-configuratie beheren met de Azure Portal, Power shell of de API?</li> </ul>|

## <a name="microsoftclassiccomputevirtualmachines"></a>micro soft. classiccompute/informatie
|Controles uitgevoerd|
|---|
|<ul><li>Wordt de hostserver actief?</li><li>Is de opstart van het hostbesturingssysteem voltooid?</li><li>Is de virtuele-machine container ingericht en ingeschakeld?</li><li>Is er netwerk verbinding tussen de host en het opslag account?</li><li>Is het starten van het gast besturingssysteem voltooid?</li><li>Is er voortdurend gepland onderhoud?</li></ul>|

## <a name="microsoftcognitiveservicesaccounts"></a>Microsoft.cognitiveservices/accounts
|Controles uitgevoerd|
|---|
|<ul><li>Kan het account worden bereikt vanuit het Data Center?</li><li>Is de Cognitive Services resource provider beschikbaar?</li><li>Is de cognitieve service beschikbaar in de juiste regio?</li><li>Kunnen Lees bewerkingen worden uitgevoerd op het opslag account met de meta gegevens van de resource?</li><li>Is de API-aanroep quota bereikt?</li><li>Is de API-aanroep Lees limiet bereikt?</li></ul>|

## <a name="microsoftcomputevirtualmachines"></a>micro soft. Compute/informatie
|Controles uitgevoerd|
|---|
|<ul><li>Wordt de server die als host fungeert voor deze virtuele machine actief?</li><li>Is de opstart van het hostbesturingssysteem voltooid?</li><li>Is de virtuele-machine container ingericht en ingeschakeld?</li><li>Is er netwerk verbinding tussen de host en het opslag account?</li><li>Is het starten van het gast besturingssysteem voltooid?</li><li>Is er voortdurend gepland onderhoud?</li></ul>|

## <a name="microsoftdatafactoryfactories"></a>Microsoft.datafactory/factories
|Controles uitgevoerd|
|---|
|<ul><li>Zijn er problemen met pijplijn uitvoeringen opgetreden?</li><li>Is het cluster dat als host fungeert voor de Data Factory in orde?</li></ul>|

## <a name="microsoftdatalakeanalyticsaccounts"></a>Microsoft.datalakeanalytics/accounts
|Controles uitgevoerd|
|---|
|<ul><li>Hebben gebruikers problemen ondervonden met het verzenden of weer geven van hun Data Lake Analytics-taken?</li><li>Kunnen Data Lake Analytics taken niet worden voltooid vanwege systeem fouten?</li></ul>|


## <a name="microsoftdatalakestoreaccounts"></a>Microsoft.datalakestore/accounts
|Controles uitgevoerd|
|---|
|<ul><li>Hebben gebruikers problemen ondervonden bij het uploaden van gegevens naar Data Lake Store?</li><li>Hebben gebruikers problemen ondervonden bij het downloaden van gegevens van Data Lake Store?</li></ul>|

## <a name="microsoftdatamigrationservices"></a>Microsoft.datamigration/services
|Controles uitgevoerd|
|---|
|<ul><li>Kan de data base Migration service niet inrichten?</li><li>Is de data base Migration service gestopt vanwege inactiviteit of gebruikers aanvraag?</li></ul>|

## <a name="microsoftdatashareaccounts"></a>Micro soft. DataShare/accounts
|Controles uitgevoerd|
|---|
|<ul><li>Is het gegevens share account actief?</li><li>Is het cluster dat als host fungeert voor de gegevens share beschikbaar?</li></ul>|

## <a name="microsoftdbformariadbservers"></a>Microsoft.DBforMariaDB/servers
|Controles uitgevoerd|
|---|
|<ul><li>Is de server niet beschikbaar vanwege onderhoud?</li><li>Is de server niet beschikbaar omdat deze opnieuw is geconfigureerd?</li></ul>|

## <a name="microsoftdbformysqlservers"></a>Microsoft.DBforMySQL/servers
|Controles uitgevoerd|
|---|
|<ul><li>Is de server niet beschikbaar vanwege onderhoud?</li><li>Is de server niet beschikbaar omdat deze opnieuw is geconfigureerd?</li></ul>|

## <a name="microsoftdbforpostgresqlservers"></a>Microsoft.DBforPostgreSQL/servers
|Controles uitgevoerd|
|---|
|<ul><li>Is de server niet beschikbaar vanwege onderhoud?</li><li>Is de server niet beschikbaar omdat deze opnieuw is geconfigureerd?</li></ul>|

## <a name="microsoftdevicesiothubs"></a>Microsoft.devices/iothubs
|Controles uitgevoerd|
|---|
|<ul><li>Wordt de IoT-hub actief gemaakt?</li></ul>|

## <a name="microsoftdocumentdbdatabaseaccounts"></a>Microsoft.documentdb/databaseAccounts
|Controles uitgevoerd|
|---|
|<ul><li>Zijn er geen data base-of verzamelings aanvragen geleverd omdat er geen Azure Cosmos DB service beschikbaar is?</li><li>Zijn er niet-beschik bare document aanvragen verzonden door een Azure Cosmos DB Service?</li></ul>|

## <a name="microsofteventhubnamespaces"></a>Microsoft.eventhub/namespaces
|Controles uitgevoerd|
|---|
|<ul><li>Is de Event Hubs naam ruimte die door de gebruiker gegenereerde fouten ondervindt?</li><li>Wordt er momenteel een upgrade uitgevoerd voor de Event Hubs-naam ruimte?</li></ul>|

## <a name="microsofthdinsightclusters"></a>Microsoft.hdinsight/clusters
|Controles uitgevoerd|
|---|
|<ul><li>Zijn er kern services beschikbaar op het HDInsight-cluster?</li><li>Kan het HDInsight-cluster de sleutel voor BYOK-versleuteling in rust benaderen?</li></ul>|

## <a name="microsoftkeyvaultvaults"></a>Microsoft.KeyVault/vaults
|Controles uitgevoerd|
|---|
|<ul><li>Worden aanvragen voor de sleutel kluis mislukt als gevolg van problemen met het Azure-kern systeem van het platform?</li><li>Worden aanvragen voor sleutel kluis beperkt vanwege te veel aanvragen van de klant?</li></ul>|

## <a name="microsoftmachinelearningwebservices"></a>Microsoft.MachineLearning/webServices
|Controles uitgevoerd|
|---|
|<ul><li>Is de web-service actief?</li></ul>|

## <a name="microsoftmediamediaservices"></a>Micro soft. Media/Media Services
|Controles uitgevoerd|
|---|
|<ul><li>Is de media service actief en wordt deze uitgevoerd?</li></ul>|

## <a name="microsoftnetworkapplicationgateways"></a>Microsoft.network/applicationgateways
|Controles uitgevoerd|
|---|
|<ul><li>Zijn de prestaties van de Application Gateway gedegradeerd?</li><li>Is de Application Gateway beschikbaar?</li></ul>|

## <a name="microsoftnetworkconnections"></a>Microsoft.network/connections
|Controles uitgevoerd|
|---|
|<ul><li>Is de VPN-tunnel verbonden?</li><li>Zijn er configuratie conflicten in de verbinding?</li><li>Zijn de vooraf gedeelde sleutels op de juiste wijze geconfigureerd?</li><li>Is het on-premises VPN-apparaat bereikbaar?</li><li>Komen er verschillen in het IPSec/IKE-beveiligings beleid?</li><li>Is de S2S-VPN-verbinding op de juiste wijze ingericht of is de status mislukt?</li><li>Is de VNET-naar-VNET-verbinding goed ingericht of is de status mislukt?</li></ul>|

## <a name="microsoftnetworkexpressroutecircuits"></a>Microsoft.network/expressroutecircuits
|Controles uitgevoerd|
|---|
|<ul><li>Is het ExpressRoute-circuit in orde?</li></ul>|

## <a name="microsoftnetworkfrontdoors"></a>micro soft. Network/frontdoors
|Controles uitgevoerd|
|---|
|<ul><li>Reageren de back-end van de front-deur met fouten aan de status tests?</li><li>Zijn de configuratie wijzigingen vertraagd?</li></ul>|

## <a name="microsoftnetworkloadbalancers"></a>Micro soft. Network/LoadBalancers
|Controles uitgevoerd|
|---|
|<ul><li>Zijn de eind punten voor taak verdeling beschikbaar?</li></ul>|

## <a name="microsoftnetworkvirtualnetworkgateways"></a>Microsoft.network/virtualNetworkGateways
|Controles uitgevoerd|
|---|
|<ul><li>Is de VPN-gateway bereikbaar vanaf het Internet?</li><li>Is de VPN Gateway in de modus stand-by?</li><li>Wordt de VPN-service op de gateway uitgevoerd?</li></ul>|

## <a name="microsoftnotificationhubsnamespace"></a>Microsoft.NotificationHubs/namespace
|Controles uitgevoerd|
|---|
|<ul><li>Kunnen runtime-bewerkingen zoals registratie, installatie of verzenden worden uitgevoerd voor de naam ruimte?</li></ul>|

## <a name="microsoftoperationalinsightsworkspaces"></a>Microsoft.operationalinsights/workspaces
|Controles uitgevoerd|
|---|
|<ul><li>Zijn er vertragingen voor het indexeren van de werk ruimte?</li></ul>|

## <a name="microsoftpowerbidedicatedcapacities"></a>Micro soft. PowerBIDedicated/capaciteiten
|Controles uitgevoerd|
|---|
|<ul><li>Is de capaciteits resource actief?</li><li>Zijn alle werk belastingen actief?</li></ul>|

## <a name="microsoftpowerbiworkspacecollections"></a>Microsoft.PowerBI/workspaceCollections
|Controles uitgevoerd|
|---|
|<ul><li>Wordt het hostbesturingssysteem geactiveerd?</li><li>Is de workspaceCollection bereikbaar vanaf buiten het Data Center?</li><li>Is de Power BI resource provider beschikbaar?</li><li>Is de Power BI-service beschikbaar in de juiste regio?</li></ul>|

## <a name="microsoftsearchsearchservices"></a>Microsoft.search/searchServices
|Controles uitgevoerd|
|---|
|<ul><li>Kunnen er diagnostische bewerkingen worden uitgevoerd op het cluster?</li></ul>|

## <a name="microsoftservicebusnamespaces"></a>Microsoft.ServiceBus/namespaces
|Controles uitgevoerd|
|---|
|<ul><li>Ondervindt klanten die door de gebruiker zijn gegenereerd Service Bus fouten?</li><li>Ondervinden gebruikers een toename van tijdelijke fouten vanwege een upgrade van de naam ruimte van Service Bus?</li></ul>|

## <a name="microsoftsqlmanagedinstancesdatabases"></a>Micro soft. SQL/managedInstances/data bases
|Controles uitgevoerd|
|---|
|<ul><li>Is de data base actief?</li></ul>|

## <a name="microsoftsqlserverdatabases"></a>Micro soft. SQL/server/data bases
|Controles uitgevoerd|
|---|
|<ul><li>Hebt u aanmeldingen bij de data base?</li></ul>|

## <a name="microsoftstoragestorageaccounts"></a>Microsoft.Storage/storageAccounts
|Controles uitgevoerd|
|---|
|<ul><li>Zijn er aanvragen voor het lezen van gegevens van het opslag account als gevolg van problemen met Azure Storage platform?</li><li>Worden aanvragen voor het schrijven van gegevens naar het opslag account mislukt als gevolg van problemen met de Azure Storage platform?</li><li>Is het opslag cluster waar het opslag account zich bevindt, niet beschikbaar?</li></ul>|

## <a name="microsoftstreamanalyticsstreamingjobs"></a>Microsoft.StreamAnalytics/streamingjobs
|Controles uitgevoerd|
|---|
|<ul><li>Zijn alle hosts waar de taak wordt uitgevoerd?</li><li>Is de taak niet gestart?</li><li>Zijn er doorlopende runtime-upgrades?</li><li>Is de taak in een verwachte status (bijvoorbeeld wordt uitgevoerd of gestopt door de klant)?</li><li>Heeft de taak uitzonde ringen in het geheugen aangetroffen?</li><li>Zijn er doorlopende geplande updates?</li><li>Is de uitvoerings beheerder (besturings plan) beschikbaar?</li></ul>|

## <a name="microsoftwebserverfarms"></a>Microsoft.web/serverFarms
|Controles uitgevoerd|
|---|
|<ul><li>Wordt de hostserver actief?</li><li>Wordt Internet Information Services uitgevoerd?</li><li>Wordt de Load Balancer uitgevoerd?</li><li>Kan het App Service plan worden bereikt binnen het Data Center?</li><li>Is het opslag account dat als host fungeert voor de site-inhoud voor de server farm beschikbaar?</li></ul>|

## <a name="microsoftwebsites"></a>Microsoft.web/sites
|Controles uitgevoerd|
|---|
|<ul><li>Wordt de hostserver actief?</li><li>Wordt Internet Information Server uitgevoerd?</li><li>Wordt de Load Balancer uitgevoerd?</li><li>Kan de web-app worden bereikt vanuit het Data Center?</li><li>Is het opslag account dat als host fungeert voor de site-inhoud beschikbaar?</li></ul>|

## <a name="next-steps"></a>Volgende stappen
-  Zie [Inleiding tot Azure service Health dash board](service-health-overview.md) en [Inleiding tot Azure resource Health](resource-health-overview.md) om meer te weten te komen over hen. 
-  [Veelgestelde vragen over Azure Resource Health](resource-health-faq.md)
- Stel waarschuwingen in zodat u op de hoogte wordt gesteld van status problemen. Zie [Configure alerts for service Health Events](../azure-monitor/platform/alerts-activity-log-service-notifications.md)(Engelstalig) voor meer informatie. 
