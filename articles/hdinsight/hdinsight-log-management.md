---
title: Logboeken beheren voor een HDInsight-cluster - Azure HDInsight
description: Bepaal de typen, formaten en bewaarbeleid voor HDInsight-activiteitslogboekbestanden.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.custom: hdinsightactive
ms.date: 02/05/2020
ms.openlocfilehash: 8c3cbf4c18b32a94abfe95e77be768020b44fda6
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79272303"
---
# <a name="manage-logs-for-an-hdinsight-cluster"></a>Logboeken beheren voor een HDInsight-cluster

Een HDInsight-cluster produceert verschillende logbestanden. Apache Hadoop en gerelateerde services, zoals Apache Spark, produceren bijvoorbeeld gedetailleerde taakuitvoeringslogboeken. Log file management is onderdeel van het onderhouden van een gezond HDInsight-cluster. Er kunnen ook wettelijke vereisten zijn voor het archiveren van logboeken.  Vanwege het aantal en de grootte van logboekbestanden helpt het optimaliseren van logboekopslag en archivering bij het beheer van servicekosten.

Het beheren van HDInsight-clusterlogboeken omvat het bewaren van informatie over alle aspecten van de clusteromgeving. Deze informatie bevat alle bijbehorende Azure Service-logboeken, clusterconfiguratie, taakuitvoeringsgegevens, foutstatussen en andere gegevens indien nodig.

Typische stappen in HDInsight log management zijn:

* Stap 1: Beleid voor logboekbehoud bepalen
* Stap 2: Configuratielogboeken voor clusterserviceversies beheren
* Stap 3: Logboekbestanden voor clustertaakuitvoering beheren
* Stap 4: Voorspelling van de grootte en kosten van de opslagvan logboeken
* Stap 5: Beleid en processen voor logboekarchieven bepalen

## <a name="step-1-determine-log-retention-policies"></a>Stap 1: Beleid voor logboekbehoud bepalen

De eerste stap bij het maken van een HDInsight-clusterlogbeheerstrategie is het verzamelen van informatie over bedrijfsscenario's en vereisten voor de opslag van taakuitvoeringsgeschiedenis.

### <a name="cluster-details"></a>Clusterdetails

De volgende clusterdetails zijn handig om informatie te verzamelen in uw strategie voor logboekbeheer. Verzamel deze informatie uit alle HDInsight-clusters die u hebt gemaakt in een bepaald Azure-account.

* Clusternaam
* Clusterregio en Azure-beschikbaarheidszone
* Clusterstatus, inclusief details van de laatste statuswijziging
* Type en aantal HDInsight-instanties die zijn opgegeven voor de hoofd-, kern- en taakknooppunten

U de meeste van deze informatie op het hoogste niveau krijgen via de Azure-portal.  U Azure [CLI](https://docs.microsoft.com/cli/azure/?view=azure-cli-latest) ook gebruiken om informatie over uw HDInsight-cluster(s) op te vragen:

```azurecli
az hdinsight list --resource-group <ResourceGroup>
az hdinsight show --resource-group <ResourceGroup> --name <ClusterName>
```

U PowerShell ook gebruiken om deze informatie te bekijken.  Zie [Apache Manage Hadoop-clusters in HDInsight voor](hdinsight-administer-use-powershell.md)meer informatie met Azure PowerShell.

### <a name="understand-the-workloads-running-on-your-clusters"></a>Inzicht in de workloads die op uw clusters worden uitgevoerd

Het is belangrijk om inzicht te krijgen in de werkbelastingtypen die op uw HDInsight-cluster(s) worden uitgevoerd om de juiste logboekstrategieën voor elk type te ontwerpen.

* Zijn de workloads experimenteel (zoals ontwikkeling of test) of productiekwaliteit?
* Hoe vaak worden de werkbelastingen van productiekwaliteit normaal uitgevoerd?
* Is een van de workloads resource-intensief en/of langlopend?
* Maakt een van de workloads gebruik van een complexe set Hadoop-services waarvoor meerdere typen logboeken worden geproduceerd?
* Heeft een van de workloads bijbehorende vereisten voor de uitvoering van regelgeving?

### <a name="example-log-retention-patterns-and-practices"></a>Voorbeeld van logretentiepatronen en -praktijken

* Overweeg het bijhouden van gegevensregelgegevens door een id toe te voegen aan elke logboekinvoer of via andere technieken. Hiermee u de oorspronkelijke bron van de gegevens en de bewerking traceren en de gegevens in elke fase volgen om de consistentie en geldigheid ervan te begrijpen.

* Bedenk hoe u logboeken verzamelen van het cluster of van meer dan één cluster en deze verzamelen voor doeleinden zoals controle, monitoring, planning en waarschuwing. U een aangepaste oplossing gebruiken om de logbestanden regelmatig te openen en te downloaden en deze te combineren en te analyseren om een dashboardweergave te bieden. U ook extra mogelijkheden toevoegen voor waarschuwingen voor beveiligings- of foutdetectie. U deze hulpprogramma's bouwen met PowerShell, de HDInsight-SDK's of code die toegang heeft tot het klassieke Azure-implementatiemodel.

* Overweeg of een bewakingsoplossing of -service een nuttig voordeel zou zijn. Het Microsoft System Center biedt een [HDInsight-beheerpakket.](https://www.microsoft.com/download/details.aspx?id=42521) U ook tools van derden gebruiken, zoals Apache Chukwa en Ganglia, om logboeken te verzamelen en te centraliseren. Veel bedrijven bieden diensten aan om hadoop-gebaseerde big data-oplossingen te monitoren, bijvoorbeeld: Centerity, Compuware APM, Sematext SPM en Zettaset Orchestrator.

## <a name="step-2-manage-cluster-service-versions-and-view-logs"></a>Stap 2: Clusterserviceversies beheren en logboeken weergeven

Een typisch HDInsight-cluster maakt gebruik van verschillende services en open-source softwarepakketten (zoals Apache HBase, Apache Spark, enzovoort). Voor sommige workloads, zoals bioinformatica, moet u mogelijk naast logboeken voor taakuitvoering ook de logboeken van de serviceconfiguratie behouden.

### <a name="view-cluster-configuration-settings-with-the-ambari-ui"></a>Clusterconfiguratie-instellingen weergeven met de Ambari-gebruikersinterface

Apache Ambari vereenvoudigt het beheer, de configuratie en de bewaking van een HDInsight-cluster door een web-gebruikersinterface en een REST-API te bieden. Ambari is opgenomen op Linux-gebaseerde HDInsight clusters. Selecteer het deelvenster **Clusterdashboard** op de pagina Azure portal HDInsight om de **koppelingspagina Clusterdashboards** te openen.  Selecteer vervolgens het **dashboardvenster van HDInsight-cluster** om de Ambari-gebruikersinterface te openen.  U wordt gevraagd om uw inloggegevens van het cluster.

Als u een lijst met serviceweergaven wilt openen, selecteert u het deelvenster **Ambari-weergaven** op de Azure-portalpagina voor HDInsight.  Deze lijst is afhankelijk van welke bibliotheken u hebt geïnstalleerd.  U bijvoorbeeld YARN Queue Manager, Hive View en Tez View zien.  Selecteer een servicekoppeling om configuratie- en servicegegevens te bekijken.  De pagina Ambari UI **Stack en Versie** bevat informatie over de configuratie- en serviceversiegeschiedenis van de clusterservices. Als u naar dit gedeelte van de Gebruikersinterface ambari wilt navigeren, selecteert u het menu **Beheerder** en vervolgens **Stapels en versies**.  Selecteer het tabblad **Versies** om de versiegegevens van de service te bekijken.

![Stapel en versies van Apache Ambari-beheerders](./media/hdinsight-log-management/ambari-stack-versions.png)

Met de Ambari-gebruikersinterface u de configuratie downloaden voor alle (of alle) services die op een bepaalde host (of knooppunt) in het cluster worden uitgevoerd.  Selecteer het menu **Hosts** en vervolgens de koppeling voor de host van interesse. Selecteer op de pagina van die host de knop **Hostactions** en **download clientconfigs**.

![Apache Ambari download host client configs](./media/hdinsight-log-management/download-client-configs.png)

### <a name="view-the-script-action-logs"></a>De scriptactielogboeken weergeven

[HdInsight-scriptacties](hdinsight-hadoop-customize-cluster-linux.md) voeren scripts uit op een cluster, handmatig of wanneer opgegeven. Scriptacties kunnen bijvoorbeeld worden gebruikt om extra software op het cluster te installeren of om configuratie-instellingen te wijzigen vanaf de standaardwaarden. Scriptactielogboeken kunnen inzicht geven in fouten die zijn opgetreden tijdens het instellen van het cluster, en ook de wijzigingen van de configuratie-instellingen die van invloed kunnen zijn op de prestaties en beschikbaarheid van het cluster.  Als u de status van een scriptactie wilt zien, selecteert u de knop **Ops** op uw Ambari-gebruikersinterface of krijgt u toegang tot de statuslogboeken in het standaardopslagaccount. De opslaglogboeken `/STORAGE_ACCOUNT_NAME/DEFAULT_CONTAINER_NAME/custom-scriptaction-logs/CLUSTER_NAME/DATE`zijn beschikbaar op .

### <a name="view-ambari-alerts-status-logs"></a>Statuslogboeken van Ambari-waarschuwingen weergeven

Apache Ambari schrijft waarschuwingsstatuswijzigingen in `ambari-alerts.log`. Het volledige `/var/log/ambari-server/ambari-alerts.log`pad is. Als u foutopsporing voor het logboek `/etc/ambari-server/conf/log4j.properties.` wilt inschakelen, `# Log alert state changes` wijzigt u een eigenschap in Wijzigen en vervolgens invoeren onder van:

```
log4j.logger.alerts=INFO,alerts

to

log4j.logger.alerts=DEBUG,alerts
```

## <a name="step-3-manage-the-cluster-job-execution-log-files"></a>Stap 3: De logboekbestanden voor clustertaakuitvoering beheren

De volgende stap is het bekijken van de taakuitvoering logboekbestanden voor de verschillende diensten.  Diensten kunnen apache HBase, Apache Spark, en vele anderen. Een Hadoop cluster produceert een groot aantal verbose logs, dus bepalen welke logs nuttig zijn (en welke niet) kan tijdrovend zijn.  Inzicht in het logging-systeem is belangrijk voor gericht beheer van logbestanden.  De volgende afbeelding is een voorbeeldlogboekbestand.

![Voorbeeld van voorbeeldlogboekbestand van HDInsight](./media/hdinsight-log-management/hdi-log-file-example.png)

### <a name="access-the-hadoop-log-files"></a>Toegang tot de Hadoop-logboekbestanden

HDInsight slaat zijn logbestanden op, zowel in het clusterbestandssysteem als in Azure Storage. U logboekbestanden in het cluster onderzoeken door een [SSH-verbinding](hdinsight-hadoop-linux-use-ssh-unix.md) met het cluster te openen en door het bestandssysteem te bladeren, of door de Hadoop YARN-statusportal op de server met het externe hoofdknooppunt te gebruiken. U de logboekbestanden in Azure Storage onderzoeken met behulp van een van de hulpprogramma's waarmee gegevens uit Azure Storage kunnen worden geopend en gedownload. Voorbeelden zijn [AzCopy,](../storage/common/storage-use-azcopy.md) [CloudXplorer](https://clumsyleaf.com/products/cloudxplorer)en de Visual Studio Server Explorer. U powershell en de Azure Storage Client-bibliotheken of de Azure .NET SDKs ook gebruiken om toegang te krijgen tot gegevens in Azure blob-opslag.

Hadoop voert het werk van de taken uit als *taakpogingen* op verschillende knooppunten in het cluster. HDInsight kan speculatieve taakpogingen starten en andere taakpogingen beëindigen die niet eerst worden voltooid. Dit genereert aanzienlijke activiteit die is aangemeld bij de controller, stderr, en syslog log bestanden on-the-fly. Bovendien worden meerdere taakpogingen tegelijkertijd uitgevoerd, maar een logboekbestand kan alleen resultaten lineair weergeven.

#### <a name="hdinsight-logs-written-to-azure-blob-storage"></a>HDInsight-logboeken die zijn geschreven naar Azure Blob-opslag

HDInsight-clusters zijn geconfigureerd om taaklogboeken te schrijven naar een Azure Blob-opslagaccount voor elke taak die wordt verzonden met behulp van de Azure PowerShell-cmdlets of de .NET-taakindienings-API's.  Als u taken via SSH inhet cluster verzendt, wordt de uitvoeringslogboekregistratiegegevens opgeslagen in de Azure-tabellen, zoals besproken in de vorige sectie.

Naast de kernlogbestanden die door HDInsight worden gegenereerd, genereren geïnstalleerde services zoals YARN ook taakuitvoeringslogboekbestanden.  Het aantal en het type logboekbestanden is afhankelijk van de geïnstalleerde services.  Algemene services zijn Apache HBase, Apache Spark, enzovoort.  Onderzoek de uitvoeringsbestanden voor taaklogboeken voor elke service om inzicht te krijgen in de algemene logboekbestanden die beschikbaar zijn op uw cluster.  Elke service heeft zijn eigen unieke methoden voor logboekregistratie en locaties voor het opslaan van logbestanden.  Als voorbeeld, details voor de toegang tot de meest voorkomende service log bestanden (van GAREN) worden besproken in de volgende sectie.

### <a name="hdinsight-logs-generated-by-yarn"></a>HDInsight logs gegenereerd door YARN

YARN verzamelt logboeken over alle containers op een werknemersknooppunt en slaat deze logboeken op als één geaggregeerd logboekbestand per werknemersknooppunt. Dat logboek wordt opgeslagen in het standaardbestandssysteem nadat een toepassing is voltooid. Uw toepassing kan honderden of duizenden containers gebruiken, maar logboeken voor alle containers die op één werknemersknooppunt worden uitgevoerd, worden altijd samengevoegd tot één bestand. Er is slechts één logboek per werknemersknooppunt dat door uw toepassing wordt gebruikt. Logaggregatie is standaard ingeschakeld op HDInsight-clusters versie 3.0 en hoger. Geaggregeerde logboeken bevinden zich in de standaardopslag voor het cluster.

```
/app-logs/<user>/logs/<applicationId>
```

De geaggregeerde logboeken zijn niet direct leesbaar, omdat ze zijn geschreven in een binaire notatie van TFile geïndexeerd per container. Gebruik de YARN ResourceManager-logboeken of CLI-hulpprogramma's om deze logboeken te bekijken als platte tekst voor toepassingen of containers die van belang zijn.

#### <a name="yarn-cli-tools"></a>GAREN CLI-gereedschappen

Als u de YARN CLI-tools wilt gebruiken, moet u eerst verbinding maken met het HDInsight-cluster via SSH. Geef `<applicationId>`de `<user-who-started-the-application>` `<containerId>`, `<worker-node-address>` , en informatie op wanneer u deze opdrachten uitvoert. U de logboeken bekijken als platte tekst met een van de volgende opdrachten:

```bash
yarn logs -applicationId <applicationId> -appOwner <user-who-started-the-application>
yarn logs -applicationId <applicationId> -appOwner <user-who-started-the-application> -containerId <containerId> -nodeAddress <worker-node-address>
```

#### <a name="yarn-resourcemanager-ui"></a>GAREN ResourceManager-gebruikersinterface

De YARN ResourceManager-gebruikersinterface wordt uitgevoerd op het clusterhoofdknooppunt en is toegankelijk via de Gebruikersinterface van het Ambari-web. Gebruik de volgende stappen om de GARENlogboeken weer te geven:

1. Navigeer in een webbrowser naar `https://CLUSTERNAME.azurehdinsight.net`. Vervang CLUSTERNAME door de naam van uw HDInsight-cluster.
2. Selecteer GAREN in de lijst met services aan de linkerkant.
3. Selecteer in de vervolgkeuzelijst Snelle koppelingen een van de clusterhoofdknooppunten en selecteer **vervolgens ResourceManager-logboeken**. Je krijgt een lijst met links naar YARN logs.

## <a name="step-4-forecast-log-volume-storage-sizes-and-costs"></a>Stap 4: Voorspelling van de grootte en kosten van de opslagvan logboeken

Nadat u de vorige stappen hebt voltooid, hebt u inzicht in de typen en volumes logboekbestanden die uw HDInsight-cluster(s) produceren.

Analyseer vervolgens het volume van loggegevens in belangrijke logopslaglocaties over een bepaalde periode. U bijvoorbeeld volume en groei analyseren over perioden van 30-60-90 dagen.  Neem deze informatie op in een spreadsheet of gebruik andere hulpprogramma's zoals Visual Studio, de Azure Storage Explorer of Power Query voor Excel. Zie [HDInsight-logboeken analyseren](hdinsight-debug-jobs.md)voor meer informatie.  

U hebt nu voldoende informatie om een logboekbeheerstrategie voor de belangrijkste logboeken te maken.  Gebruik uw spreadsheet (of tool naar keuze) om de groei van de logboekgrootte en de azure-servicekosten voor logboekopslag in de toekomst te voorspellen.  Overweeg ook eventuele vereisten voor het bewaren van logboeken voor de set logboeken die u onderzoekt.  Nu u toekomstige opslagkosten voor logboeken opnieuw voorspellen, nadat u hebt bepaald welke logboekbestanden kunnen worden verwijderd (indien aanwezig) en welke logboeken moeten worden bewaard en gearchiveerd naar goedkopere Azure Storage.

## <a name="step-5-determine-log-archive-policies-and-processes"></a>Stap 5: Beleid en processen voor logboekarchieven bepalen

Nadat u hebt bepaald welke logbestanden kunnen worden verwijderd, u logboekregistratieparameters op veel Hadoop-services aanpassen om logbestanden na een bepaalde periode automatisch te verwijderen.

Voor bepaalde logboekbestanden u een goedkopere benadering voor het archiveren van logboekbestanden gebruiken. Voor activiteitenlogboeken van Azure Resource Manager u deze aanpak verkennen met behulp van de Azure-portal.  Archivering van de resourcebeheerlogboeken instellen door de koppeling **Activiteitslogboek** in de Azure-portal voor uw HDInsight-exemplaar te selecteren.  Selecteer boven aan de zoekpagina van het activiteitenlogboek het **menu-item Exporteren** om het **logboekvenster voor activiteit exporteren** te openen.  Vul het abonnement, de regio in, of u wilt exporteren naar een opslagaccount en hoeveel dagen u de logboeken wilt behouden. In hetzelfde deelvenster u ook aangeven of u wilt exporteren naar een gebeurtenishub.

![Voorbeeld van het logboek voor exportactiviteiten van Azure-portal](./media/hdinsight-log-management/hdi-export-log-files.png)

U ook logboekarchivering met PowerShell uitvoeren.  Zie [Azure Automation-logboeken archiveren in Azure Blob Storage](https://gallery.technet.microsoft.com/scriptcenter/Archive-Azure-Automation-898a1aa8)voor een voorbeeld van PowerShell-script.

### <a name="accessing-azure-storage-metrics"></a>Toegang tot Azure Storage-statistieken

Azure Storage kan worden geconfigureerd om opslagbewerkingen en -toegang te registreren. U deze zeer gedetailleerde logboeken gebruiken voor capaciteitsbewaking en -planning en voor controleaanvragen voor opslag. De geregistreerde informatie bevat latentiedetails, zodat u de prestaties van uw oplossingen controleren en verfijnen.
U de .NET SDK voor Hadoop gebruiken om de logboekbestanden te onderzoeken die zijn gegenereerd voor de Azure Storage die de gegevens bevat voor een HDInsight-cluster.

### <a name="control-the-size-and-number-of-backup-indexes-for-old-log-files"></a>De grootte en het aantal back-upindexen voor oude logboekbestanden beheren

Als u de grootte en het aantal bewaarde logboekbestanden `RollingFileAppender`wilt beheren, stelt u de volgende eigenschappen in van :

* `maxFileSize`is de kritieke grootte van het bestand, waarboven het bestand wordt gerold. De standaardwaarde is 10 MB.
* `maxBackupIndex`hiermee wordt het aantal back-upbestanden opgegeven dat moet worden gemaakt, standaard 1.

### <a name="other-log-management-techniques"></a>Andere technieken voor logboekbeheer

Om te voorkomen dat de schijfruimte opraakt, u een aantal besturingssysteemhulpprogramma's gebruiken, zoals [logboekdraaien](https://linux.die.net/man/8/logrotate) om de verwerking van logboekbestanden te beheren. U kunt `logrotate` configureren om te draaien op een dagelijkse basis, het comprimeren van logbestanden en het verwijderen van oude. Uw aanpak is afhankelijk van uw vereisten, zoals hoe lang de logboekbestanden op lokale knooppunten moeten worden opgeslagen.  

U ook controleren of DEBUG-logboekregistratie is ingeschakeld voor een of meer services, waardoor de grootte van het uitvoerlogboek aanzienlijk toeneemt.  

Als u de logboeken van alle knooppunten naar één centrale locatie wilt verzamelen, u een gegevensstroom maken, zoals het opnemen van alle logboekvermeldingen in Solr.

## <a name="next-steps"></a>Volgende stappen

* [Monitoring en logging praktijk voor HDInsight](https://msdn.microsoft.com/library/dn749790.aspx)
* [Toegang Apache Hadoop YARN applicatie logs in Linux-gebaseerde HDInsight](hdinsight-hadoop-access-yarn-app-logs-linux.md)
* [Hoe de grootte van logbestanden voor verschillende Apache Hadoop-componenten te beheren](https://community.hortonworks.com/articles/8882/how-to-control-size-of-log-files-for-various-hdp-c.html)
