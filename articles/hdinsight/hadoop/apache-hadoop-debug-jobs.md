---
title: 'Debug Apache Hadoop: logboeken weer geven en fout berichten interpreteren-Azure HDInsight'
description: Meer informatie over de fout berichten die u kunt ontvangen bij het beheren van HDInsight met behulp van Power shell en stappen die u moet uitvoeren om te herstellen.
ms.reviewer: jasonh
author: ashishthaps
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 11/14/2017
ms.author: ashishth
ms.openlocfilehash: 847b36c2aab761383a4a25bd4da5c626c4744ce1
ms.sourcegitcommit: 4c3d6c2657ae714f4a042f2c078cf1b0ad20b3a4
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 10/25/2019
ms.locfileid: "72935479"
---
# <a name="analyze-apache-hadoop-logs-in-azure-hdinsight"></a>Apache Hadoop-Logboeken in azure HDInsight analyseren

Elk Apache Hadoop cluster in azure HDInsight heeft een Azure-opslag account dat wordt gebruikt als het standaard bestandssysteem. Het opslag account wordt aangeduid als het standaard opslag account. Cluster maakt gebruik van de Azure-tabel opslag en de Blob-opslag op het standaard opslag account om de logboeken op te slaan.  Zie [Apache Hadoop clusters in HDInsight beheren](../hdinsight-administer-use-portal-linux.md#find-the-storage-accounts)voor meer informatie over het standaard opslag account voor uw cluster. De logboeken blijven bewaard in het opslag account, zelfs nadat het cluster is verwijderd.

## <a name="logs-written-to-azure-tables"></a>Logboeken die zijn geschreven naar Azure-tabellen

De logboeken die zijn geschreven naar Azure-tabellen, bieden één inzicht in wat er gebeurt met een HDInsight-cluster.

Wanneer u een HDInsight-cluster maakt, worden er automatisch zes tabellen gemaakt voor op Linux gebaseerde clusters in de standaard tabel opslag:

* hdinsightagentlog
* syslog
* daemonlog
* hadoopservicelog
* ambariserverlog
* ambariagentlog

De namen van de tabel bestanden zijn **u\<cluster naam > DDMonYYYYatHHMMSSsss\<tablename >** .

Deze tabellen bevatten de volgende velden:

* ClusterDnsName
* ComponentName
* EventTimestamp
* Host
* MALoggingHash
* Bericht
* N
* PreciseTimeStamp
* Rol
* Rij
* Tenant
* Neem
* TraceLevel

### <a name="tools-for-accessing-the-logs"></a>Hulpprogram ma's voor toegang tot de logboeken
Er zijn veel hulpprogram ma's beschikbaar om toegang te krijgen tot gegevens in deze tabellen:

* Visual Studio
* Azure Storage-verkenner
* Power Query voor Excel

#### <a name="use-power-query-for-excel"></a>Power Query gebruiken voor Excel
Power Query kunnen worden geïnstalleerd vanuit [Microsoft Power query voor Excel](https://www.microsoft.com/en-us/download/details.aspx?id=39379). Zie de download pagina voor de systeem vereisten.

**Power Query gebruiken om het service logboek te openen en te analyseren**

1. Open **micro soft Excel**.
2. Klik in het menu **Power query** op **van Azure**en klik vervolgens op **uit Microsoft Azure Table-opslag**.
   
    ![HDInsight Hadoop Excel PowerQuery open Azure-tabel opslag](./media/apache-hadoop-debug-jobs/hdinsight-hadoop-analyze-logs-using-excel-power-query-open.png)
3. Voer de naam van het opslag account in, hetzij de korte naam ofwel de FQDN.
4. Voer de sleutel voor het opslag account in. Er wordt een lijst met tabellen weer geven:
   
    ![HDInsight Hadoop-logboeken die zijn opgeslagen in azure-tabel opslag](./media/apache-hadoop-debug-jobs/hdinsight-hadoop-analyze-logs-table-names.png)
5. Klik met de rechter muisknop op de tabel hadoopservicelog in het deel venster **Navigator** en selecteer **bewerken**. Er worden vier kolommen weer geven. U kunt eventueel de kolom **partitie sleutel**, **rij-sleutel**en **tijds tempel** verwijderen door deze te selecteren en vervolgens op **kolommen verwijderen** te klikken in de opties in het lint.
6. Klik op het pictogram uitvouwen in de kolom inhoud om de kolommen te kiezen die u wilt importeren in het Excel-werk blad. Voor deze demonstratie heb ik de TraceLevel en de naam van het onderdeel gekozen: het kan me enkele basis informatie geven over welke onderdelen problemen hadden.
   
    ![HDInsight Hadoop-logboeken Kies kolommen Excel](./media/apache-hadoop-debug-jobs/hdinsight-hadoop-analyze-logs-using-excel-power-query-filter.png "HDInsight Hadoop-logboeken Kies kolommen Excel")
7. Klik op **OK** om de gegevens te importeren.
8. Selecteer de kolommen **TraceLevel**, Role en **componentnaam** en klik vervolgens op **Group by** Control in het lint.
9. Klik op **OK** in het dialoog venster groeperen op
10. Klik op * * & sluiten * * Toep assen.

U kunt Excel nu gebruiken om te filteren en te sorteren als dat nodig is. U kunt ook andere kolommen (zoals bericht) toevoegen om in te zoomen op problemen wanneer deze zich voordoen, maar het selecteren en groeperen van de hierboven beschreven kolommen biedt een goede afbeelding van wat er gebeurt met Hadoop-Services. Hetzelfde idee kan worden toegepast op de tabellen setuplog en hadoopinstalllog.

#### <a name="use-visual-studio"></a>Visual Studio gebruiken
**Visual Studio gebruiken**

1. Open Visual Studio.
2. Klik in het menu **weer gave** op **Cloud Explorer**. U kunt ook gewoon op **CTRL +\, CTRL + X**klikken.
3. Selecteer **resource typen**in **Cloud Explorer**.  De andere beschik bare optie is **resource groepen**.
4. Vouw **opslag accounts**, het standaard opslag account voor uw cluster en vervolgens de **tabellen**uit.
5. Dubbel klik op **hadoopservicelog**.
6. Een filter toevoegen. Bijvoorbeeld:
   
        TraceLevel eq 'ERROR'
   
    ![HDInsight Hadoop-logboeken Kies kolommen versus](./media/apache-hadoop-debug-jobs/hdinsight-hadoop-analyze-logs-visual-studio-filter.png "HDInsight Hadoop-logboeken Kies kolommen versus")
   
    Zie voor meer informatie over het maken van filters [teken reeksen maken voor de tabelontwerpfunctie](../../vs-azure-tools-table-designer-construct-filter-strings.md).

## <a name="logs-written-to-azure-blob-storage"></a>Logboeken geschreven naar Azure Blob Storage

De logboeken die zijn geschreven naar Azure-tabellen, bieden één inzicht in wat er gebeurt met een HDInsight-cluster. Deze tabellen bieden echter geen logboeken op taak niveau, wat nuttig kan zijn bij het inzoomen op problemen die zich voordoen. HDInsight-clusters worden geconfigureerd voor het schrijven van de taak logboeken naar uw Blob Storage-account voor elke taak die wordt verzonden via Templeton om dit volgende detail niveau op te geven. Dit betekent nagenoeg taken die worden verzonden met behulp van de Microsoft Azure PowerShell-cmdlets of de .NET job Submission-Api's, niet voor taken die worden verzonden via RDP/opdracht regel toegang tot het cluster. 

Als u de logboeken wilt weer geven, raadpleegt u [toegang Apache HADOOP garens van toepassings logboeken op HDInsight op basis van Linux](../hdinsight-hadoop-access-yarn-app-logs-linux.md).

## <a name="view-cluster-health-and-job-logs"></a>Cluster status-en taak logboeken weer geven

### <a name="access-the-ambari-ui"></a>Toegang tot de Ambari-gebruikers interface

Klik in het Azure Portal op de naam van een HDInsight-cluster om het deel venster cluster te openen. Klik in het deel venster cluster op **dash board**.

![HDInsight-cluster dashboard starten](./media/apache-hadoop-debug-jobs/hdi-debug-launch-dashboard.png)


### <a name="access-the-yarn-ui"></a>Toegang tot de garen-UI
Klik in het Azure Portal op de naam van een HDInsight-cluster om het deel venster cluster te openen. Klik in het deel venster cluster op **dash board**. Voer de referenties van de Cluster beheerder in wanneer dit wordt gevraagd. Selecteer in Ambari de optie **garens** in de lijst met services aan de linkerkant. Selecteer op de pagina die wordt weer gegeven, de optie **snelle koppelingen**en selecteer vervolgens de actieve hoofd knooppunt vermelding en de Resource Manager-gebruikers interface.

U kunt de gebruikers interface van garen gebruiken om het volgende te doen:

* **Cluster status ophalen**. Vouw in het linkerdeel venster **cluster**uit en klik op **info**. Dit zijn de details van de cluster status, zoals het totale toegewezen geheugen, de gebruikte kernen, de status van de cluster resource manager, de Cluster versie, enzovoort.
  
    ![HDInsight-cluster dashboard garens starten](./media/apache-hadoop-debug-jobs/hdi-debug-yarn-cluster-state.png "HDInsight-cluster dashboard garens starten")
* **Status van knoop punt ophalen**. Vouw in het linkerdeel venster **cluster**uit en klik op **knoop punten**. Hier vindt u een lijst met alle knoop punten in het cluster, het HTTP-adres van elk knoop punt, de resources die zijn toegewezen aan elk knoop punt, enzovoort.
* **Taak status bewaken**. Vouw in het linkerdeel venster **cluster**uit en klik vervolgens op **toepassingen** om alle taken in het cluster weer te geven. Als u taken in een bepaalde status wilt bekijken (zoals nieuw, verzonden, actief, enzovoort), klikt u op de juiste koppeling onder **toepassingen**. U kunt verder klikken op de naam van de taak om meer te weten te komen over de taak, zoals de uitvoer, logboeken, enzovoort.

### <a name="access-the-hbase-ui"></a>Toegang tot de HBase-gebruikers interface
Klik in het Azure Portal op de naam van een HDInsight HBase-cluster om het deel venster cluster te openen. Klik in het deel venster cluster op **dash board**. Voer de referenties van de Cluster beheerder in wanneer dit wordt gevraagd. Selecteer in Ambari HBase in de lijst met Services. Selecteer **snelle koppelingen** boven aan de pagina, wijs de actieve Zookeeper-knooppunt koppeling aan en klik vervolgens op HBase Master gebruikers interface.

## <a name="hdinsight-error-codes"></a>HDInsight-fout codes
De fout berichten die in deze sectie zijn opgenomen, zijn bedoeld om de gebruikers van Hadoop in azure HDInsight inzicht te geven in mogelijke fout voorwaarden die ze kunnen tegen komen bij het beheer van de service met Azure PowerShell en om hen te informeren over de stappen die kunnen worden uitgevoerd herstellen van de fout.

Sommige van deze fout berichten kunnen ook worden weer gegeven in de Azure Portal wanneer deze wordt gebruikt voor het beheren van HDInsight-clusters. Maar andere fout berichten die u kunt tegen komen, zijn minder gedetailleerd als gevolg van de beperkingen voor de mogelijke herstel acties in deze context. Andere fout berichten worden weer gegeven in de contexten waar de oplossing duidelijk is. 

### <a id="AtLeastOneSqlMetastoreMustBeProvided"></a>AtLeastOneSqlMetastoreMustBeProvided
* **Beschrijving**: geef Azure SQL database Details voor ten minste één onderdeel op om aangepaste instellingen te kunnen gebruiken voor Hive-en Oozie-meta Stores.
* **Beperking**: de gebruiker moet een geldig SQL Azure-meta Store opgeven en de aanvraag opnieuw proberen.  

### <a id="AzureRegionNotSupported"></a>AzureRegionNotSupported
* **Beschrijving**: kan het cluster niet maken in de regio *nameOfYourRegion*. Gebruik een geldige HDInsight-regio en voer de aanvraag opnieuw uit.
* **Risico beperking**: de klant moet de cluster regio maken die deze momenteel ondersteunt: Zuidoost-azië, Europa-west, Europa-Noord, VS-Oost of VS-West.  

### <a id="ClusterContainerRecordNotFound"></a>ClusterContainerRecordNotFound
* **Beschrijving**: de server kan de aangevraagde cluster record niet vinden.  
* **Risico beperking**: Voer de bewerking opnieuw uit.

### <a id="ClusterDnsNameInvalidReservedWord"></a>ClusterDnsNameInvalidReservedWord
* **Beschrijving**: de cluster-DNS-naam *yourDnsName* is ongeldig. Zorg ervoor dat de naam begint en eindigt met alfanumeriek en alleen '-' speciaal teken mag bevatten  
* **Risico beperking**: Zorg ervoor dat u een geldige DNS-naam voor uw cluster hebt gebruikt dat begint en eindigt met alfanumeriek en geen speciale tekens bevat die geen deel uitmaken van het streepje '-' en voer de bewerking vervolgens opnieuw uit.

### <a id="ClusterNameUnavailable"></a>ClusterNameUnavailable
* **Beschrijving**: de cluster naam *yourClusterName* is niet beschikbaar. Kies een andere naam.  
* **Risico beperking**: de gebruiker moet een cluster naam opgeven die uniek is en die niet bestaat en het opnieuw probeert. Als de gebruiker de portal gebruikt, wordt deze door de gebruikers interface op de hoogte gesteld als er al een cluster naam wordt gebruikt tijdens het maken van de stappen.

### <a id="ClusterPasswordInvalid"></a>ClusterPasswordInvalid
* **Beschrijving**: het wacht woord voor het cluster is ongeldig. Het wacht woord moet uit Mini maal tien tekens bestaan en moet ten minste één cijfer, hoofd letter, kleine letter en speciaal teken bevatten zonder spaties en mag niet de gebruikers naam bevatten als onderdeel daarvan.  
* **Risico beperking**: Geef een geldig cluster wachtwoord op en voer de bewerking opnieuw uit.

### <a id="ClusterUserNameInvalid"></a>ClusterUserNameInvalid
* **Beschrijving**: de cluster gebruikersnaam is ongeldig. Controleer of de gebruikers naam geen speciale tekens of spaties bevat.  
* **Risico beperking**: Geef een geldige cluster gebruikersnaam op en voer de bewerking opnieuw uit.

### <a id="ClusterUserNameInvalidReservedWord"></a>ClusterUserNameInvalidReservedWord
* **Beschrijving**: de cluster-DNS-naam *yourDnsClusterName* is ongeldig. Zorg ervoor dat de naam begint en eindigt met alfanumeriek en alleen '-' speciaal teken mag bevatten  
* **Risico beperking**: Geef een geldige gebruikers naam voor het DNS-cluster op en voer de bewerking opnieuw uit.

### <a id="ContainerNameMisMatchWithDnsName"></a>ContainerNameMisMatchWithDnsName
* **Beschrijving**: de container naam in URI *yourcontainerURI* en DNS-naam *yourDnsName* in de hoofd tekst van de aanvraag moet hetzelfde zijn.  
* **Risico beperking**: Zorg ervoor dat de naam van de container en de DNS-naam hetzelfde zijn en probeer het opnieuw.

### <a id="DataNodeDefinitionNotFound"></a>DataNodeDefinitionNotFound
* **Beschrijving**: ongeldige cluster configuratie. Kan geen gegevens knooppunt definities vinden in de knooppunt grootte.  
* **Risico beperking**: Voer de bewerking opnieuw uit.

### <a id="DeploymentDeletionFailure"></a>DeploymentDeletionFailure
* **Beschrijving**: het verwijderen van de implementatie is mislukt voor het cluster  
* **Risico beperking**: Voer de Verwijder bewerking opnieuw uit.

### <a id="DnsMappingNotFound"></a>DnsMappingNotFound
* **Beschrijving**: fout in de service configuratie. De vereiste DNS-toewijzings gegevens zijn niet gevonden.  
* **Risico beperking**: cluster verwijderen en een nieuw cluster maken.

### <a id="DuplicateClusterContainerRequest"></a>DuplicateClusterContainerRequest
* **Beschrijving**: er is een dubbele poging tot het maken van een cluster container. Er bestaat een record voor *nameOfYourContainer* , maar eTags komen niet overeen.
* **Risico beperking**: Geef een unieke naam op voor de container en voer de bewerking maken opnieuw uit.

### <a id="DuplicateClusterInHostedService"></a>DuplicateClusterInHostedService
* **Beschrijving**: de gehoste service *nameOfYourHostedService* bevat al een cluster. Een gehoste service kan niet meerdere clusters bevatten  
* **Risico beperking**: host het cluster in een andere gehoste service.

### <a id="FailureToUpdateDeploymentStatus"></a>FailureToUpdateDeploymentStatus
* **Beschrijving**: de server kan de status van de cluster implementatie niet bijwerken.  
* **Risico beperking**: Voer de bewerking opnieuw uit. Als dit meerdere keren gebeurt, neemt u contact op met CSS.

### <a id="HdiRestoreClusterAltered"></a>HdiRestoreClusterAltered
* **Beschrijving**: het cluster *yourClusterName* is verwijderd als onderdeel van het onderhoud. Maak het cluster opnieuw.
* **Risico beperking**: Maak het cluster opnieuw.

### <a id="HeadNodeConfigNotFound"></a>HeadNodeConfigNotFound
* **Beschrijving**: ongeldige cluster configuratie. De vereiste configuratie van het hoofd knooppunt is niet gevonden in de knooppunt grootten.
* **Risico beperking**: Voer de bewerking opnieuw uit.

### <a id="HostedServiceCreationFailure"></a>HostedServiceCreationFailure
* **Beschrijving**: kan de *nameOfYourHostedService*van de gehoste service niet maken. Voer de aanvraag opnieuw uit.  
* **Risico beperking**: Voer de aanvraag opnieuw uit.

### <a id="HostedServiceHasProductionDeployment"></a>HostedServiceHasProductionDeployment
* **Beschrijving**: de *nameOfYourHostedService* van de gehoste service heeft al een productie-implementatie. Een gehoste service kan niet meerdere productie-implementaties bevatten. Voer de aanvraag opnieuw uit met een andere cluster naam.
* **Risico beperking**: gebruik een andere cluster naam en voer de aanvraag opnieuw uit.

### <a id="HostedServiceNotFound"></a>HostedServiceNotFound
* **Beschrijving**: de *nameOfYourHostedService* voor de gehoste service voor het cluster is niet gevonden.  
* **Risico beperking**: als het cluster de status fout heeft, verwijdert u het en probeert u het opnieuw.

### <a id="HostedServiceWithNoDeployment"></a>HostedServiceWithNoDeployment
* **Beschrijving**: de gehoste service *nameOfYourHostedService* heeft geen gekoppelde implementatie.  
* **Risico beperking**: als het cluster de status fout heeft, verwijdert u het en probeert u het opnieuw.

### <a id="InsufficientResourcesCores"></a>InsufficientResourcesCores
* **Beschrijving**: de abonnements- *yourSubscriptionId* heeft geen kern geheugens om cluster *yourClusterName*te maken. Vereist: *resourcesRequired*, beschikbaar: *resourcesAvailable*.  
* **Risico beperking**: Maak bronnen vrij in uw abonnement of verg root de beschik bare resources voor het abonnement en probeer het cluster opnieuw te maken.

### <a id="InsufficientResourcesHostedServices"></a>InsufficientResourcesHostedServices
* **Beschrijving**: de abonnements-id *yourSubscriptionId* heeft geen quota voor een nieuwe HostedService om cluster *yourClusterName*te maken.  
* **Risico beperking**: Maak bronnen vrij in uw abonnement of verg root de beschik bare resources voor het abonnement en probeer het cluster opnieuw te maken.

### <a id="InternalErrorRetryRequest"></a>InternalErrorRetryRequest
* **Beschrijving**: er is een interne fout opgetreden op de server. Voer de aanvraag opnieuw uit.  
* **Risico beperking**: Voer de aanvraag opnieuw uit.

### <a id="InvalidAzureStorageLocation"></a>InvalidAzureStorageLocation
* **Beschrijving**: Azure Storage locatie *dataRegionName* is geen geldige locatie. Zorg ervoor dat de regio juist is en voer de aanvraag opnieuw uit.
* **Risico beperking**: Selecteer een opslag locatie die HDInsight ondersteunt, Controleer of uw cluster is opgeslagen en voer de bewerking opnieuw uit.

### <a id="InvalidNodeSizeForDataNode"></a>InvalidNodeSizeForDataNode
* **Beschrijving**: ongeldige VM-grootte voor gegevens knooppunten. Alleen de grootte van de grote virtuele machine wordt ondersteund voor alle gegevens knooppunten.  
* **Risico beperking**: Geef de ondersteunde knooppunt grootte voor het gegevens knooppunt op en voer de bewerking opnieuw uit.

### <a id="InvalidNodeSizeForHeadNode"></a>InvalidNodeSizeForHeadNode
* **Beschrijving**: ongeldige VM-grootte voor het hoofd knooppunt. Alleen de grootte van de ExtraLarge-VM wordt ondersteund voor het hoofd knooppunt.  
* **Risico beperking**: Geef de ondersteunde knooppunt grootte voor het hoofd knooppunt op en voer de bewerking opnieuw uit

### <a id="InvalidRightsForDeploymentDeletion"></a>InvalidRightsForDeploymentDeletion
* **Beschrijving**: de *yourSubscriptionId* die wordt gebruikt, beschikt niet over voldoende machtigingen om de verwijderings bewerking voor de cluster- *yourClusterName*uit te voeren.  
* **Risico beperking**: als het cluster de status fout heeft, verwijdert u het en probeert u het opnieuw.  

### <a id="InvalidStorageAccountBlobContainerName"></a>InvalidStorageAccountBlobContainerName
* **Beschrijving**: de naam van de BLOB-container van het externe opslag account *yourContainerName* is ongeldig. Zorg ervoor dat de naam begint met een letter en alleen kleine letters, cijfers en koppel tekens bevat.  
* **Risico beperking**: Geef een geldige naam op voor de BLOB-container van het opslag account en voer de bewerking opnieuw uit.

### <a id="InvalidStorageAccountConfigurationSecretKey"></a>InvalidStorageAccountConfigurationSecretKey
* **Beschrijving**: de configuratie voor het *yourStorageAccountName* van het externe opslag account is vereist om de details van geheime sleutels in te stellen.  
* **Risico beperking**: Geef een geldige geheime sleutel voor het opslag account op en voer de bewerking opnieuw uit.

### <a id="InvalidVersionHeaderFormat"></a>InvalidVersionHeaderFormat
* **Beschrijving**: de versie-header *yourVersionHeader* heeft niet de geldige indeling jjjj-mm-dd.  
* **Risico beperking**: Geef een geldige indeling voor de versie-header op en voer de aanvraag opnieuw uit.

### <a id="MoreThanOneHeadNode"></a>MoreThanOneHeadNode
* **Beschrijving**: ongeldige cluster configuratie. Er is meer dan één configuratie van het hoofd knooppunt gevonden.  
* **Risico beperking**: Bewerk de configuratie zodat er slechts één hoofd knooppunt wordt opgegeven.

### <a id="OperationTimedOutRetryRequest"></a>OperationTimedOutRetryRequest
* **Beschrijving**: de bewerking kan niet worden voltooid binnen de toegestane tijd of het maximale aantal nieuwe pogingen mogelijk. Voer de aanvraag opnieuw uit.  
* **Risico beperking**: Voer de aanvraag opnieuw uit.

### <a id="ParameterNullOrEmpty"></a>ParameterNullOrEmpty
* **Beschrijving**: de para meter *yourParameterName* kan niet null of leeg zijn.  
* **Beperking**: Geef een geldige waarde op voor de para meter.

### <a id="PreClusterCreationValidationFailure"></a>PreClusterCreationValidationFailure
* **Beschrijving**: een of meer invoer aanvragen voor het maken van een cluster zijn niet geldig. Zorg ervoor dat de invoer waarden juist zijn en voer de aanvraag opnieuw uit.  
* **Risico beperking**: Zorg ervoor dat de invoer waarden juist zijn en voer de aanvraag opnieuw uit.

### <a id="RegionCapabilityNotAvailable"></a>RegionCapabilityNotAvailable
* **Beschrijving**: de regio mogelijkheid is niet beschikbaar voor de regio *YourRegionName* en de *yourSubscriptionId*van de abonnements-id.  
* **Risico beperking**: Geef een regio op die HDInsight-clusters ondersteunt. De openbaar ondersteunde regio's zijn: Zuidoost-Azië, Europa-west, Europa-noord, VS-Oost of VS-West.

### <a id="StorageAccountNotColocated"></a>StorageAccountNotColocated
* **Beschrijving**: het *yourStorageAccountName* voor het opslag account bevindt zich in de regio *currentRegionName*. Deze moet hetzelfde zijn als de cluster regio *yourClusterRegionName*.  
* **Risico beperking**: Geef een opslag account op in dezelfde regio als uw cluster of als uw gegevens al aanwezig zijn in het opslag account, maakt u een nieuw cluster in dezelfde regio als het bestaande opslag account. Als u de portal gebruikt, wordt de gebruikers interface hiervan op de hoogte gesteld van dit probleem.

### <a id="SubscriptionIdNotActive"></a>SubscriptionIdNotActive
* **Beschrijving**: de opgegeven abonnements-id *yourSubscriptionId* is niet actief.  
* **Risico beperking**: Activeer uw abonnement opnieuw of ontvang een nieuw geldig abonnement.

### <a id="SubscriptionIdNotFound"></a>SubscriptionIdNotFound
* **Beschrijving**: de *yourSubscriptionId* van het abonnement is niet gevonden.  
* **Risico beperking**: Controleer of uw abonnement-id geldig is en voer de bewerking opnieuw uit.

### <a id="UnableToResolveDNS"></a>UnableToResolveDNS
* **Beschrijving**: kan DNS- *yourDnsUrl*niet omzetten. Controleer of de volledig gekwalificeerde URL voor het BLOB-eind punt is opgegeven.  
* **Risico beperking**: Geef een geldige BLOB-URL op. De URL moet volledig geldig zijn, met inbegrip van *http://* en eindigend in *. com*.

### <a id="UnableToVerifyLocationOfResource"></a>UnableToVerifyLocationOfResource
* **Beschrijving**: de locatie van de resource- *yourDnsUrl*kan niet worden gecontroleerd. Controleer of de volledig gekwalificeerde URL voor het BLOB-eind punt is opgegeven.  
* **Risico beperking**: Geef een geldige BLOB-URL op. De URL moet volledig geldig zijn, met inbegrip van *http://* en eindigend in *. com*.

### <a id="VersionCapabilityNotAvailable"></a>VersionCapabilityNotAvailable
* **Beschrijving**: de versie mogelijkheid is niet beschikbaar voor versie *SpecifiedVersion* en de *yourSubscriptionId*van de abonnements-id.  
* **Risico beperking**: Kies een versie die beschikbaar is en voer de bewerking opnieuw uit.

### <a id="VersionNotSupported"></a>VersionNotSupported
* **Beschrijving**: versie *specifiedVersion* niet ondersteund.
* **Risico beperking**: Kies een versie die wordt ondersteund en voer de bewerking opnieuw uit.

### <a id="VersionNotSupportedInRegion"></a>VersionNotSupportedInRegion
* **Beschrijving**: versie *specifiedVersion* is niet beschikbaar in de Azure-regio *specifiedRegion*.  
* **Risico beperking**: Kies een versie die wordt ondersteund in de opgegeven regio en voer de bewerking opnieuw uit.

### <a id="WasbAccountConfigNotFound"></a>WasbAccountConfigNotFound
* **Beschrijving**: ongeldige cluster configuratie. De vereiste configuratie van het WASB-account is niet gevonden in de externe accounts.  
* **Risico beperking**: Controleer of het account bestaat en correct is opgegeven in de configuratie en voer de bewerking opnieuw uit.

## <a name="next-steps"></a>Volgende stappen

* [Heap-dumps inschakelen voor Apache Hadoop Services op HDInsight op basis van Linux](../hdinsight-hadoop-collect-debug-heap-dump-linux.md)
* [HDInsight-clusters beheren met behulp van de Apache Ambari-webinterface](../hdinsight-hadoop-manage-ambari.md)
