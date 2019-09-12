---
title: 'Fout opsporing Apache Hadoop: Logboeken weer geven en fout berichten interpreteren-Azure HDInsight'
description: Meer informatie over de fout berichten die u kunt ontvangen bij het beheren van HDInsight met behulp van Power shell en stappen die u moet uitvoeren om te herstellen.
ms.reviewer: jasonh
author: ashishthaps
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 11/14/2017
ms.author: ashishth
ms.openlocfilehash: 751d5b47006f5c99a747503ad4f052b3e03a043c
ms.sourcegitcommit: 7c5a2a3068e5330b77f3c6738d6de1e03d3c3b7d
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 09/11/2019
ms.locfileid: "70882428"
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

De namen van de tabel bestanden zijn **u\<clustername >\<DDMonYYYYatHHMMSSsss TableName >** .

Deze tabellen bevatten de volgende velden:

* ClusterDnsName
* ComponentName
* EventTimestamp
* Host
* MALoggingHash
* Message
* N
* PreciseTimeStamp
* Role
* RowIndex
* Tenant
* TIMESTAMP
* TraceLevel

### <a name="tools-for-accessing-the-logs"></a>Hulpprogram ma's voor toegang tot de logboeken
Er zijn veel hulpprogram ma's beschikbaar om toegang te krijgen tot gegevens in deze tabellen:

* Visual Studio
* Azure Opslagverkenner
* Power Query voor Excel

#### <a name="use-power-query-for-excel"></a>Power Query gebruiken voor Excel
Power Query kunnen worden geïnstalleerd vanuit [Microsoft Power query voor Excel](https://www.microsoft.com/en-us/download/details.aspx?id=39379). Zie de download pagina voor de systeem vereisten.

**Power Query gebruiken om het service logboek te openen en te analyseren**

1. Open **Microsoft Excel**.
2. Klik in het menu **Power query** op **van Azure**en klik vervolgens op **uit Microsoft Azure Table-opslag**.
   
    ![HDInsight Hadoop Excel PowerQuery open Azure-tabel opslag](./media/apache-hadoop-debug-jobs/hdinsight-hadoop-analyze-logs-using-excel-power-query-open.png)
3. Voer de naam van het opslag account in, hetzij de korte naam ofwel de FQDN.
4. Voer de sleutel voor het opslag account in. Er wordt een lijst met tabellen weer geven:
   
    ![HDInsight Hadoop-logboeken die zijn opgeslagen in azure-tabel opslag](./media/apache-hadoop-debug-jobs/hdinsight-hadoop-analyze-logs-table-names.png)
5. Klik met de rechter muisknop op de tabel hadoopservicelog in het deel venster **Navigator** en selecteer **bewerken**. Er worden vier kolommen weer geven. U kunt eventueel de kolom **partitie sleutel**, **rij-sleutel**en **tijds tempel** verwijderen door deze te selecteren en vervolgens op **kolommen verwijderen** te klikken in de opties in het lint.
6. Klik op het pictogram uitvouwen in de kolom inhoud om de kolommen te kiezen die u wilt importeren in het Excel-werk blad. Voor deze demonstratie kiest u TraceLevel en componentnaam: Het kan me enkele basis informatie geven over welke onderdelen problemen ondervonden.
   
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


Zie voor meer informatie over toepassings logboeken [het beheer van gebruikers logboeken en toegang in Apache HADOOP garens vereenvoudigen](https://hortonworks.com/blog/simplifying-user-logs-management-and-access-in-yarn/).


## <a name="view-cluster-health-and-job-logs"></a>Cluster status-en taak logboeken weer geven
### <a name="access-the-ambari-ui"></a>Toegang tot de Ambari-gebruikers interface
Klik in het Azure Portal op de naam van een HDInsight-cluster om het deel venster cluster te openen. Klik in het deel venster cluster op **dash board**.

![Cluster dashboard starten](./media/apache-hadoop-debug-jobs/hdi-debug-launch-dashboard.png)


### <a name="access-the-yarn-ui"></a>Toegang tot de garen-UI
Klik in het Azure Portal op de naam van een HDInsight-cluster om het deel venster cluster te openen. Klik in het deel venster cluster op **dash board**. Voer de referenties van de Cluster beheerder in wanneer dit wordt gevraagd. Selecteer in Ambari de optie **garens** in de lijst met services aan de linkerkant. Selecteer op de pagina die wordt weer gegeven, de optie **snelle koppelingen**en selecteer vervolgens de actieve hoofd knooppunt vermelding en de Resource Manager-gebruikers interface.

U kunt de gebruikers interface van garen gebruiken om het volgende te doen:

* **Cluster status ophalen**. Vouw in het linkerdeel venster **cluster**uit en klik op **info**. Dit zijn de details van de cluster status, zoals het totale toegewezen geheugen, de gebruikte kernen, de status van de cluster resource manager, de Cluster versie, enzovoort.
  
    ![Cluster dashboard garens starten](./media/apache-hadoop-debug-jobs/hdi-debug-yarn-cluster-state.png "Cluster dashboard garens starten")
* **Status van knoop punt ophalen**. Vouw in het linkerdeel venster **cluster**uit en klik op **knoop punten**. Hier vindt u een lijst met alle knoop punten in het cluster, het HTTP-adres van elk knoop punt, de resources die zijn toegewezen aan elk knoop punt, enzovoort.
* **Taak status bewaken**. Vouw in het linkerdeel venster **cluster**uit en klik vervolgens op **toepassingen** om alle taken in het cluster weer te geven. Als u taken in een bepaalde status wilt bekijken (zoals nieuw, verzonden, actief, enzovoort), klikt u op de juiste koppeling onder **toepassingen**. U kunt verder klikken op de naam van de taak om meer te weten te komen over de taak, zoals de uitvoer, logboeken, enzovoort.

### <a name="access-the-hbase-ui"></a>Toegang tot de HBase-gebruikers interface
Klik in het Azure Portal op de naam van een HDInsight HBase-cluster om het deel venster cluster te openen. Klik in het deel venster cluster op **dash board**. Voer de referenties van de Cluster beheerder in wanneer dit wordt gevraagd. Selecteer in Ambari HBase in de lijst met Services. Selecteer **snelle koppelingen** boven aan de pagina, wijs de actieve Zookeeper-knooppunt koppeling aan en klik vervolgens op HBase Master gebruikers interface.

## <a name="hdinsight-error-codes"></a>HDInsight-fout codes
De fout berichten die in deze sectie zijn opgenomen, zijn bedoeld om de gebruikers van Hadoop in azure HDInsight inzicht te geven in mogelijke fout voorwaarden die ze kunnen tegen komen bij het beheer van de service met Azure PowerShell en om hen te informeren over de stappen die kunnen worden uitgevoerd herstellen van de fout.

Sommige van deze fout berichten kunnen ook worden weer gegeven in de Azure Portal wanneer deze wordt gebruikt voor het beheren van HDInsight-clusters. Maar andere fout berichten die u kunt tegen komen, zijn minder gedetailleerd als gevolg van de beperkingen voor de mogelijke herstel acties in deze context. Andere fout berichten worden weer gegeven in de contexten waar de oplossing duidelijk is. 

### <a id="AtLeastOneSqlMetastoreMustBeProvided"></a>AtLeastOneSqlMetastoreMustBeProvided
* **Beschrijving**: Geef Azure SQL database gegevens voor ten minste één onderdeel op om aangepaste instellingen te kunnen gebruiken voor Hive-en Oozie-meta Stores.
* **Risico beperking**: De gebruiker moet een geldig SQL Azure-meta Store opgeven en de aanvraag opnieuw proberen.  

### <a id="AzureRegionNotSupported"></a>AzureRegionNotSupported
* **Beschrijving**: Kan het cluster niet maken in de regio *nameOfYourRegion*. Gebruik een geldige HDInsight-regio en voer de aanvraag opnieuw uit.
* **Risico beperking**: De klant moet de cluster regio maken die deze momenteel ondersteunt: Zuidoost-Azië, Europa-west, Europa-noord, VS-Oost of VS-West.  

### <a id="ClusterContainerRecordNotFound"></a>ClusterContainerRecordNotFound
* **Beschrijving**: De server kan de aangevraagde cluster record niet vinden.  
* **Risico beperking**: Voer de bewerking opnieuw uit.

### <a id="ClusterDnsNameInvalidReservedWord"></a>ClusterDnsNameInvalidReservedWord
* **Beschrijving**: De naam van het cluster-DNS- *yourDnsName* is ongeldig. Zorg ervoor dat de naam begint en eindigt met alfanumeriek en alleen '-' speciaal teken mag bevatten  
* **Risico beperking**: Zorg ervoor dat u een geldige DNS-naam voor uw cluster hebt gebruikt dat begint en eindigt met alfanumeriek en geen speciale tekens bevat die geen deel uitmaken van het streepje '-' en voer de bewerking vervolgens opnieuw uit.

### <a id="ClusterNameUnavailable"></a>ClusterNameUnavailable
* **Beschrijving**: De cluster naam *yourClusterName* is niet beschikbaar. Kies een andere naam.  
* **Risico beperking**: De gebruiker moet een cluster naam opgeven die uniek is en die niet bestaat en opnieuw proberen. Als de gebruiker de portal gebruikt, wordt deze door de gebruikers interface op de hoogte gesteld als er al een cluster naam wordt gebruikt tijdens het maken van de stappen.

### <a id="ClusterPasswordInvalid"></a>ClusterPasswordInvalid
* **Beschrijving**: Het cluster wachtwoord is ongeldig. Het wacht woord moet uit Mini maal tien tekens bestaan en moet ten minste één cijfer, hoofd letter, kleine letter en speciaal teken bevatten zonder spaties en mag niet de gebruikers naam bevatten als onderdeel daarvan.  
* **Risico beperking**: Geef een geldig cluster wachtwoord op en voer de bewerking opnieuw uit.

### <a id="ClusterUserNameInvalid"></a>ClusterUserNameInvalid
* **Beschrijving**: De gebruikers naam van het cluster is ongeldig. Controleer of de gebruikers naam geen speciale tekens of spaties bevat.  
* **Risico beperking**: Geef een geldige cluster gebruikers naam op en voer de bewerking opnieuw uit.

### <a id="ClusterUserNameInvalidReservedWord"></a>ClusterUserNameInvalidReservedWord
* **Beschrijving**: De naam van het cluster-DNS- *yourDnsClusterName* is ongeldig. Zorg ervoor dat de naam begint en eindigt met alfanumeriek en alleen '-' speciaal teken mag bevatten  
* **Risico beperking**: Geef een geldige gebruikers naam voor het DNS-cluster op en voer de bewerking opnieuw uit.

### <a id="ContainerNameMisMatchWithDnsName"></a>ContainerNameMisMatchWithDnsName
* **Beschrijving**: De container naam in URI *yourcontainerURI* en DNS-naam *yourDnsName* in de hoofd tekst van de aanvraag moet hetzelfde zijn.  
* **Risico beperking**: Zorg ervoor dat de naam van de container en de DNS-naam hetzelfde zijn en probeer het opnieuw.

### <a id="DataNodeDefinitionNotFound"></a>DataNodeDefinitionNotFound
* **Beschrijving**: Ongeldige cluster configuratie. Kan geen gegevens knooppunt definities vinden in de knooppunt grootte.  
* **Risico beperking**: Voer de bewerking opnieuw uit.

### <a id="DeploymentDeletionFailure"></a>DeploymentDeletionFailure
* **Beschrijving**: Het verwijderen van de implementatie is mislukt voor het cluster  
* **Risico beperking**: Voer de Verwijder bewerking opnieuw uit.

### <a id="DnsMappingNotFound"></a>DnsMappingNotFound
* **Beschrijving**: Fout met Service configuratie. De vereiste DNS-toewijzings gegevens zijn niet gevonden.  
* **Risico beperking**: Verwijder het cluster en maak een nieuw cluster.

### <a id="DuplicateClusterContainerRequest"></a>DuplicateClusterContainerRequest
* **Beschrijving**: Poging tot het maken van een dubbele cluster container. Er bestaat een record voor *nameOfYourContainer* , maar eTags komen niet overeen.
* **Risico beperking**: Geef een unieke naam op voor de container en voer de bewerking maken opnieuw uit.

### <a id="DuplicateClusterInHostedService"></a>DuplicateClusterInHostedService
* **Beschrijving**: De *nameOfYourHostedService* van de gehoste service bevat al een cluster. Een gehoste service kan niet meerdere clusters bevatten  
* **Risico beperking**: Host het cluster in een andere gehoste service.

### <a id="FailureToUpdateDeploymentStatus"></a>FailureToUpdateDeploymentStatus
* **Beschrijving**: De server kan de status van de cluster implementatie niet bijwerken.  
* **Risico beperking**: Voer de bewerking opnieuw uit. Als dit meerdere keren gebeurt, neemt u contact op met CSS.

### <a id="HdiRestoreClusterAltered"></a>HdiRestoreClusterAltered
* **Beschrijving**: De cluster- *yourClusterName* is verwijderd als onderdeel van het onderhoud. Maak het cluster opnieuw.
* **Risico beperking**: Maak het cluster opnieuw.

### <a id="HeadNodeConfigNotFound"></a>HeadNodeConfigNotFound
* **Beschrijving**: Ongeldige cluster configuratie. De vereiste configuratie van het hoofd knooppunt is niet gevonden in de knooppunt grootten.
* **Risico beperking**: Voer de bewerking opnieuw uit.

### <a id="HostedServiceCreationFailure"></a>HostedServiceCreationFailure
* **Beschrijving**: Kan de gehoste service *nameOfYourHostedService*niet maken. Voer de aanvraag opnieuw uit.  
* **Risico beperking**: Voer de aanvraag opnieuw uit.

### <a id="HostedServiceHasProductionDeployment"></a>HostedServiceHasProductionDeployment
* **Beschrijving**: De *nameOfYourHostedService* van de gehoste service heeft al een productie-implementatie. Een gehoste service kan niet meerdere productie-implementaties bevatten. Voer de aanvraag opnieuw uit met een andere cluster naam.
* **Risico beperking**: Gebruik een andere naam voor het cluster en voer de aanvraag opnieuw uit.

### <a id="HostedServiceNotFound"></a>HostedServiceNotFound
* **Beschrijving**: De *nameOfYourHostedService* voor de gehoste service voor het cluster is niet gevonden.  
* **Risico beperking**: Als het cluster de status fout heeft, verwijdert u het en probeert u het opnieuw.

### <a id="HostedServiceWithNoDeployment"></a>HostedServiceWithNoDeployment
* **Beschrijving**: De *nameOfYourHostedService* van de gehoste service heeft geen gekoppelde implementatie.  
* **Risico beperking**: Als het cluster de status fout heeft, verwijdert u het en probeert u het opnieuw.

### <a id="InsufficientResourcesCores"></a>InsufficientResourcesCores
* **Beschrijving**: De abonnements- *yourSubscriptionId* heeft geen kern geheugens meer om cluster *yourClusterName*te maken. Vereist: *resourcesRequired*, beschikbaar: *resourcesAvailable*.  
* **Risico beperking**: Maak resources vrij in uw abonnement of verg root de beschik bare resources voor het abonnement en probeer het cluster opnieuw te maken.

### <a id="InsufficientResourcesHostedServices"></a>InsufficientResourcesHostedServices
* **Beschrijving**: De abonnements-ID *yourSubscriptionId* heeft geen quota voor een nieuwe HostedService om een cluster *yourClusterName*te maken.  
* **Risico beperking**: Maak resources vrij in uw abonnement of verg root de beschik bare resources voor het abonnement en probeer het cluster opnieuw te maken.

### <a id="InternalErrorRetryRequest"></a>InternalErrorRetryRequest
* **Beschrijving**: Er is een interne fout opgetreden op de server. Voer de aanvraag opnieuw uit.  
* **Risico beperking**: Voer de aanvraag opnieuw uit.

### <a id="InvalidAzureStorageLocation"></a>InvalidAzureStorageLocation
* **Beschrijving**: Azure Storage locatie *dataRegionName* is geen geldige locatie. Zorg ervoor dat de regio juist is en voer de aanvraag opnieuw uit.
* **Risico beperking**: Selecteer een opslag locatie die HDInsight ondersteunt, Controleer of uw cluster is opgeslagen en voer de bewerking opnieuw uit.

### <a id="InvalidNodeSizeForDataNode"></a>InvalidNodeSizeForDataNode
* **Beschrijving**: Ongeldige VM-grootte voor gegevens knooppunten. Alleen de grootte van de grote virtuele machine wordt ondersteund voor alle gegevens knooppunten.  
* **Risico beperking**: Geef de ondersteunde knooppunt grootte voor het gegevens knooppunt op en voer de bewerking opnieuw uit.

### <a id="InvalidNodeSizeForHeadNode"></a>InvalidNodeSizeForHeadNode
* **Beschrijving**: Ongeldige VM-grootte voor het hoofd knooppunt. Alleen de grootte van de ExtraLarge-VM wordt ondersteund voor het hoofd knooppunt.  
* **Risico beperking**: Geef de ondersteunde knooppunt grootte voor het hoofd knooppunt op en voer de bewerking opnieuw uit

### <a id="InvalidRightsForDeploymentDeletion"></a>InvalidRightsForDeploymentDeletion
* **Beschrijving**: De abonnements *-id die wordt gebruikt* , beschikt niet over voldoende machtigingen om de verwijderings bewerking voor de cluster- *yourClusterName*uit te voeren.  
* **Risico beperking**: Als het cluster de status fout heeft, verwijdert u het en probeert u het opnieuw.  

### <a id="InvalidStorageAccountBlobContainerName"></a>InvalidStorageAccountBlobContainerName
* **Beschrijving**: De naam van de BLOB-container van het externe opslag account *yourContainerName* is ongeldig. Zorg ervoor dat de naam begint met een letter en alleen kleine letters, cijfers en koppel tekens bevat.  
* **Risico beperking**: Geef een geldige naam op voor de BLOB-container van het opslag account en voer de bewerking opnieuw uit.

### <a id="InvalidStorageAccountConfigurationSecretKey"></a>InvalidStorageAccountConfigurationSecretKey
* **Beschrijving**: De configuratie voor het *yourStorageAccountName* van het externe opslag account is vereist om de details van geheime sleutels in te stellen.  
* **Risico beperking**: Geef een geldige geheime sleutel voor het opslag account op en voer de bewerking opnieuw uit.

### <a id="InvalidVersionHeaderFormat"></a>InvalidVersionHeaderFormat
* **Beschrijving**: De versie header *yourVersionHeader* heeft geen geldige indeling van jjjj-mm-dd.  
* **Risico beperking**: Geef een geldige indeling voor de versie-header op en voer de aanvraag opnieuw uit.

### <a id="MoreThanOneHeadNode"></a>MoreThanOneHeadNode
* **Beschrijving**: Ongeldige cluster configuratie. Er is meer dan één configuratie van het hoofd knooppunt gevonden.  
* **Risico beperking**: Bewerk de configuratie zodat er slechts één hoofd knooppunt wordt opgegeven.

### <a id="OperationTimedOutRetryRequest"></a>OperationTimedOutRetryRequest
* **Beschrijving**: De bewerking kan niet worden voltooid binnen de toegestane tijd of het maximale aantal nieuwe pogingen dat mogelijk is. Voer de aanvraag opnieuw uit.  
* **Risico beperking**: Voer de aanvraag opnieuw uit.

### <a id="ParameterNullOrEmpty"></a>ParameterNullOrEmpty
* **Beschrijving**: De para meter *yourParameterName* kan niet null of leeg zijn.  
* **Risico beperking**: Geef een geldige waarde op voor de para meter.

### <a id="PreClusterCreationValidationFailure"></a>PreClusterCreationValidationFailure
* **Beschrijving**: Een of meer invoer aanvragen voor het maken van een cluster zijn niet geldig. Zorg ervoor dat de invoer waarden juist zijn en voer de aanvraag opnieuw uit.  
* **Risico beperking**: Zorg ervoor dat de invoer waarden juist zijn en voer de aanvraag opnieuw uit.

### <a id="RegionCapabilityNotAvailable"></a>RegionCapabilityNotAvailable
* **Beschrijving**: De regio mogelijkheid is niet beschikbaar voor de regio- *yourRegionName* en de *YourSubscriptionId*van de abonnements-id.  
* **Risico beperking**: Geef een regio op die HDInsight-clusters ondersteunt. De openbaar ondersteunde regio's zijn: Zuidoost-Azië, Europa-west, Europa-noord, VS-Oost of VS-West.

### <a id="StorageAccountNotColocated"></a>StorageAccountNotColocated
* **Beschrijving**: Het *yourStorageAccountName* voor het opslag account bevindt zich in de regio *currentRegionName*. Deze moet hetzelfde zijn als de cluster regio *yourClusterRegionName*.  
* **Risico beperking**: Geef een opslag account op in dezelfde regio als uw cluster of als uw gegevens al aanwezig zijn in het opslag account, maakt u een nieuw cluster in dezelfde regio als het bestaande opslag account. Als u de portal gebruikt, wordt de gebruikers interface hiervan op de hoogte gesteld van dit probleem.

### <a id="SubscriptionIdNotActive"></a>SubscriptionIdNotActive
* **Beschrijving**: De opgegeven abonnements-ID *yourSubscriptionId* is niet actief.  
* **Risico beperking**: Activeer uw abonnement opnieuw of ontvang een nieuw geldig abonnement.

### <a id="SubscriptionIdNotFound"></a>SubscriptionIdNotFound
* **Beschrijving**: Kan de *yourSubscriptionId* van het abonnement-id niet vinden.  
* **Risico beperking**: Controleer of uw abonnement-ID geldig is en voer de bewerking opnieuw uit.

### <a id="UnableToResolveDNS"></a>UnableToResolveDNS
* **Beschrijving**: Kan DNS- *yourDnsUrl*niet omzetten. Controleer of de volledig gekwalificeerde URL voor het BLOB-eind punt is opgegeven.  
* **Risico beperking**: Geef een geldige BLOB-URL op. De URL moet volledig geldig zijn, met inbegrip van *http://* en eindigend in *. com*.

### <a id="UnableToVerifyLocationOfResource"></a>UnableToVerifyLocationOfResource
* **Beschrijving**: De locatie van de resource- *yourDnsUrl*kan niet worden gecontroleerd. Controleer of de volledig gekwalificeerde URL voor het BLOB-eind punt is opgegeven.  
* **Risico beperking**: Geef een geldige BLOB-URL op. De URL moet volledig geldig zijn, met inbegrip van *http://* en eindigend in *. com*.

### <a id="VersionCapabilityNotAvailable"></a>VersionCapabilityNotAvailable
* **Beschrijving**: De versie mogelijkheid is niet beschikbaar voor versie *specifiedVersion* en de *YourSubscriptionId*van de abonnements-id.  
* **Risico beperking**: Kies een versie die beschikbaar is en voer de bewerking opnieuw uit.

### <a id="VersionNotSupported"></a>VersionNotSupported
* **Beschrijving**: Versie *specifiedVersion* wordt niet ondersteund.
* **Risico beperking**: Kies een versie die wordt ondersteund en voer de bewerking opnieuw uit.

### <a id="VersionNotSupportedInRegion"></a>VersionNotSupportedInRegion
* **Beschrijving**: Versie *specifiedVersion* is niet beschikbaar in de Azure-regio *specifiedRegion*.  
* **Risico beperking**: Kies een versie die wordt ondersteund in de opgegeven regio en voer de bewerking opnieuw uit.

### <a id="WasbAccountConfigNotFound"></a>WasbAccountConfigNotFound
* **Beschrijving**: Ongeldige cluster configuratie. De vereiste configuratie van het WASB-account is niet gevonden in de externe accounts.  
* **Risico beperking**: Controleer of het account bestaat en correct is opgegeven in de configuratie en voer de bewerking opnieuw uit.

## <a name="next-steps"></a>Volgende stappen

* [Heap-dumps inschakelen voor Apache Hadoop Services op HDInsight op basis van Linux](../hdinsight-hadoop-collect-debug-heap-dump-linux.md)
* [HDInsight-clusters beheren met behulp van de Apache Ambari-webinterface](../hdinsight-hadoop-manage-ambari.md)
