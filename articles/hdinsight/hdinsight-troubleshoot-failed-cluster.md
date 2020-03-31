---
title: Een langzame of mislukte taak oplossen op Azure HDInsight-cluster
description: Een diagnose en probleemoplossing voor een langzame of mislukte taak op een Azure HDInsight-cluster.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: troubleshooting
ms.date: 08/15/2019
ms.openlocfilehash: be991b63784a2c72a51bfbdc8506f3b4695ed6c7
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75895321"
---
# <a name="troubleshoot-a-slow-or-failing-job-on-a-hdinsight-cluster"></a>Problemen met een trage of niet werkende taak in een HDInsight-cluster oplossen

Als een toepassing die gegevens verwerkt op een HDInsight-cluster traag of mislukt met een foutcode, hebt u verschillende probleemoplossingsopties. Als het langer duurt om uw taken uit te voeren dan verwacht, of als u in het algemeen trage responstijden ziet, kunnen er storingen stroomopwaarts vanuit uw cluster optreden, zoals de services waarop het cluster wordt uitgevoerd. Echter, de meest voorkomende oorzaak van deze vertragingen is onvoldoende schaalvergroting. Wanneer u een nieuw HDInsight-cluster maakt, selecteert u de juiste [virtuele machineformaten](hdinsight-component-versioning.md#default-node-configuration-and-virtual-machine-sizes-for-clusters).

Als u een langzaam of mislukt cluster wilt diagnosticeren, verzamelt u informatie over alle aspecten van de omgeving, zoals bijbehorende Azure Services, clusterconfiguratie en taakuitvoeringsgegevens. Een handige diagnose is om te proberen de foutstatus op een ander cluster te reproduceren.

* Stap 1: Verzamel gegevens over het probleem.
* Stap 2: Valideer de HDInsight-clusteromgeving.
* Stap 3: Bekijk de status van uw cluster.
* Stap 4: Bekijk de omgevingsstack en -versies.
* Stap 5: De clusterlogboekbestanden onderzoeken.
* Stap 6: Controleer de configuratie-instellingen.
* Stap 7: De fout reproduceren op een ander cluster.

## <a name="step-1-gather-data-about-the-issue"></a>Stap 1: Gegevens verzamelen over het probleem

HDInsight biedt veel tools die u gebruiken om problemen met clusters te identificeren en op te lossen. De volgende stappen begeleiden u door deze tools en geven suggesties voor het lokaliseren van het probleem.

### <a name="identify-the-problem"></a>Het probleem identificeren

Houd de volgende vragen om het probleem te identificeren:

* Wat had ik verwacht te gebeuren? Wat gebeurde er in plaats daarvan?
* Hoe lang duurde het proces om uit te voeren? Hoe lang had het moeten lopen?
* Zijn mijn taken altijd traag uitgevoerd op dit cluster? Hebben ze sneller draaien op een ander cluster?
* Wanneer is dit probleem voor het eerst opgetreden? Hoe vaak is het sindsdien gebeurd?
* Is er iets veranderd in mijn clusterconfiguratie?

### <a name="cluster-details"></a>Clusterdetails

Belangrijke clusterinformatie omvat:

* Clusternaam.
* Clusterregio - controleer op [regiostoringen](https://azure.microsoft.com/status/).
* HDInsight-clustertype en -versie.
* Type en aantal HDInsight-instanties die zijn opgegeven voor de hoofd- en werknemersknooppunten.

De Azure-portal kan deze informatie verstrekken:

![HDInsight Azure-portalgegevens](./media/hdinsight-troubleshoot-failed-cluster/hdi-azure-portal-info.png)

U [Azure CLI](https://docs.microsoft.com/cli/azure/?view=azure-cli-latest)ook gebruiken:

```azurecli
az hdinsight list --resource-group <ResourceGroup>
az hdinsight show --resource-group <ResourceGroup> --name <ClusterName>
```

Een andere optie is het gebruik van PowerShell. Zie [Apache Hadoop-clusters beheren in HDInsight met Azure PowerShell](hdinsight-administer-use-powershell.md)voor meer informatie.

## <a name="step-2-validate-the-hdinsight-cluster-environment"></a>Stap 2: De CLUSTERomgeving hdInsight valideren

Elk HDInsight-cluster is gebaseerd op verschillende Azure-services en op open-sourcesoftware zoals Apache HBase en Apache Spark. HDInsight-clusters kunnen ook een beroep doen op andere Azure-services, zoals Azure Virtual Networks.  Een clusterfout kan worden veroorzaakt door een van de lopende services op uw cluster of door een externe service.  Een configuratiewijziging van de clusterservice kan er ook toe leiden dat het cluster mislukt.

### <a name="service-details"></a>Servicegegevens

* Controleer de releaseversies van de open source-bibliotheek.
* Controleer op [Azure Service-uitval](https://azure.microsoft.com/status/).  
* Controleer op azure servicegebruikslimieten. 
* Controleer de subnetconfiguratie van Azure Virtual Network.  

### <a name="view-cluster-configuration-settings-with-the-ambari-ui"></a>Clusterconfiguratie-instellingen weergeven met de Ambari-gebruikersinterface

Apache Ambari biedt beheer en monitoring van een HDInsight-cluster met een web-gebruikersinterface en een REST-API. Ambari is opgenomen op Linux-gebaseerde HDInsight clusters. Selecteer het deelvenster **Clusterdashboard** op de pagina HDInsight van Azure portal.  Selecteer het **dashboardvenster van het HDInsight-cluster** om de Ambari-gebruikersinterface te openen en voer de inloggegevens van het cluster in.  

![Overzicht van apache Ambari-dashboard](./media/hdinsight-troubleshoot-failed-cluster/apache-ambari-overview.png)

Als u een lijst met serviceweergaven wilt openen, selecteert u **Ambari-weergaven** op de Azure-portalpagina.  Deze lijst is afhankelijk van welke bibliotheken zijn geïnstalleerd. U bijvoorbeeld YARN Queue Manager, Hive View en Tez View zien.  Selecteer een servicekoppeling om configuratie- en servicegegevens te bekijken.

#### <a name="check-for-azure-service-outages"></a>Controleren op Azure-servicestoringen

HDInsight is afhankelijk van verschillende Azure-services. Het voert virtuele servers uit op Azure HDInsight, slaat gegevens en scripts op in Azure Blob-opslag of Azure Data Lake Storage en indexeert logboekbestanden in Azure Table-opslag. Onderbrekingen van deze services, hoewel zeldzaam, kunnen problemen veroorzaken in HDInsight. Als er onverwachte vertragingen of fouten in uw cluster optreden, schakelt u het [Azure Status Dashboard in.](https://azure.microsoft.com/status/) De status van elke service wordt per regio weergegeven. Controleer de regio van uw cluster en ook de regio's voor gerelateerde services.

#### <a name="check-azure-service-usage-limits"></a>Azure-servicegebruikslimieten controleren

Als u een groot cluster start of veel clusters tegelijk hebt gestart, kan een cluster mislukken als u een Azure-servicelimiet hebt overschreden. Servicelimieten variëren, afhankelijk van uw Azure-abonnement. Zie [Azure-abonnement- en servicelimieten, quota en beperkingen](https://docs.microsoft.com/azure/azure-resource-manager/management/azure-subscription-service-limits) voor meer informatie.
U Microsoft verzoeken het aantal beschikbare HDInsight-resources (zoals VM-cores en VM-exemplaren) te verhogen met een aanvraag voor het verhogen van de [kernquotumvan Resourcebeheer.](https://docs.microsoft.com/azure/azure-portal/supportability/resource-manager-core-quotas-request)

#### <a name="check-the-release-version"></a>Controleer de releaseversie

Vergelijk de clusterversie met de nieuwste HDInsight-release. Elke HDInsight-release bevat verbeteringen zoals nieuwe toepassingen, functies, patches en bugfixes. Het probleem dat van invloed is op uw cluster is mogelijk opgelost in de nieuwste versie. Voer uw cluster indien mogelijk opnieuw uit met de nieuwste versie van HDInsight en bijbehorende bibliotheken zoals Apache HBase, Apache Spark en anderen.

#### <a name="restart-your-cluster-services"></a>Uw clusterservices opnieuw starten

Als u vertragingen in uw cluster ondervindt, u overwegen uw services opnieuw op te starten via de Ambari-gebruikersinterface of de Azure Classic CLI. Het cluster kan tijdelijke fouten ondervinden en opnieuw opstarten is de snelste manier om uw omgeving te stabiliseren en mogelijk de prestaties te verbeteren.

## <a name="step-3-view-your-clusters-health"></a>Stap 3: De status van uw cluster weergeven

HDInsight-clusters bestaan uit verschillende typen knooppunten die worden uitgevoerd op virtuele machine-exemplaren. Elk knooppunt kan worden gecontroleerd op resourcehonger, problemen met de netwerkconnectiviteit en andere problemen die het cluster kunnen vertragen. Elk cluster bevat twee hoofdknooppunten en de meeste clustertypen bevatten een combinatie van werk- en randknooppunten. 

Zie [Clusters instellen in HDInsight met Apache Hadoop, Apache Spark, Apache Kafka en meer](hdinsight-hadoop-provision-linux-clusters.md)voor een beschrijving van de verschillende knooppunten die elk clustertype gebruikt.

In de volgende secties wordt beschreven hoe u de status van elk knooppunt en van het totale cluster controleren.

### <a name="get-a-snapshot-of-the-cluster-health-using-the-ambari-ui-dashboard"></a>Een momentopname van de clusterstatus bekijken met het Ambari UI-dashboard

Het [Ambari UI-dashboard](#view-cluster-configuration-settings-with-the-ambari-ui) (`https://<clustername>.azurehdinsight.net`) biedt een overzicht van de clusterstatus, zoals uptime, geheugen, netwerk- en CPU-gebruik, HDFS-schijfgebruik, enzovoort. Gebruik het gedeelte Hosts van Ambari om bronnen op hostniveau weer te geven. U services ook stoppen en opnieuw opstarten.

### <a name="check-your-webhcat-service"></a>Controleer uw WebHCat-service

Een veelvoorkomend scenario voor Apache Hive, Apache Pig of Apache Sqoop-taken die niet werken, is een fout met de [WebHCat-service](hdinsight-hadoop-templeton-webhcat-debug-errors.md) (of *Templeton).* WebHCat is een REST-interface voor uitvoering op afstand, zoals Hive, Pig, Scoop en MapReduce. WebHCat vertaalt de aanvragen voor het indienen van vacatures naar Apache Hadoop YARN-toepassingen en retourneert een status die is afgeleid van de YARN-toepassingsstatus.  In de volgende secties worden veelvoorkomende WebHCat HTTP-statuscodes beschreven.

#### <a name="badgateway-502-status-code"></a>BadGateway (502 statuscode)

Deze code is een algemeen bericht van gatewayknooppunten en is de meest voorkomende foutstatuscodes. Een mogelijke oorzaak hiervoor is de WebHCat-service die op het actieve hoofdknooppunt staat. Gebruik de volgende opdracht CURL om deze mogelijkheid te controleren:

```bash
curl -u admin:{HTTP PASSWD} https://{CLUSTERNAME}.azurehdinsight.net/templeton/v1/status?user.name=admin
```

Ambari geeft een waarschuwing weer waarop de hosts worden weergegeven waarop de WebHCat-service is uitgeschakeld. U proberen de WebHCat-service opnieuw te back-upen door de service opnieuw op de host te starten.

![Apache Ambari start WebHCat-server opnieuw op](./media/hdinsight-troubleshoot-failed-cluster/restart-webhcat-server.png)

Als een WebHCat-server nog steeds niet opduikt, controleert u het logboek voor mislukte berichten. Voor meer gedetailleerde informatie, controleer de `stderr` en `stdout` bestanden waarnaar wordt verwezen op het knooppunt.

#### <a name="webhcat-times-out"></a>WebHCat heeft een uit-dag

Een HDInsight Gateway vergeeft reacties die langer `502 BadGateway`dan twee minuten duren en keren terug. WebHCat vraagt YARN-services op voor taakstatussen en als YARN langer dan twee minuten duurt om te reageren, kan dat verzoek een time-out krijgen.

Bekijk in dit geval de `/var/log/webhcat` volgende logboeken in de map:

* **webhcat.log** is de log4j log op welke server schrijft logs
* **webhcat-console.log** is de stdout van de server wanneer gestart
* **webhcat-console-error.log** is de stderr van het serverproces

> [!NOTE]  
> Elk `webhcat.log` wordt dagelijks overgerold en `webhcat.log.YYYY-MM-DD`genereert bestanden met de naam . Selecteer het juiste bestand voor het tijdsbereik dat u onderzoekt.

In de volgende secties worden enkele mogelijke oorzaken voor WebHCat-time-outs beschreven.

##### <a name="webhcat-level-timeout"></a>Time-out op WebHCat-niveau

Wanneer WebHCat onder belasting is, met meer dan 10 open sockets, duurt het langer om nieuwe socketverbindingen tot stand te brengen, wat kan resulteren in een time-out. Als u de netwerkverbindingen van en `netstat` naar WebHCat wilt weergeven, gebruikt u op de huidige actieve headnode:

```bash
netstat | grep 30111
```

30111 is de poort waar WebHCat op luistert. Het aantal open stopcontacten moet minder dan 10 zijn.

Als er geen open sockets zijn, levert de vorige opdracht geen resultaat op. Gebruik als u wilt controleren of Templeton op staat en luistert op poort 30111:

```bash
netstat -l | grep 30111
```

##### <a name="yarn-level-timeout"></a>TIME-out yarnniveau

Templeton roept YARN op om taken uit te voeren, en de communicatie tussen Templeton en YARN kan een time-out veroorzaken.

Op garenniveau zijn er twee soorten time-outs:

1. Het indienen van een YARN taak kan lang genoeg duren om een time-out veroorzaken.

    Als u `/var/log/webhcat/webhcat.log` het logboekbestand opent en zoekt naar 'taak in de wachtrij', ziet u mogelijk meerdere vermeldingen waarbij de uitvoeringstijd te lang is (>2000 ms), met vermeldingen die steeds langere wachttijden weergeven.

    De tijd voor de in de wachtrij staande taken blijft toenemen omdat de snelheid waarmee nieuwe taken worden ingediend hoger is dan het tempo waarmee de oude taken worden voltooid. Zodra het YARN-geheugen 100% is gebruikt, kan de *joblauncher-wachtrij* geen capaciteit meer lenen uit de *standaardwachtrij.* Daarom kunnen er geen nieuwe taken meer worden geaccepteerd in de joblauncher-wachtrij. Dit gedrag kan ertoe leiden dat de wachttijd langer en langer wordt, waardoor een time-outfout ontstaat die meestal door vele anderen wordt gevolgd.

    De volgende afbeelding toont de joblauncher wachtrij op 714,4% te veel gebruikt. Dit is acceptabel zolang er nog vrije capaciteit in de standaardwachtrij is om van te lenen. Echter, wanneer het cluster volledig wordt gebruikt en het YARN-geheugen op 100% capaciteit is, moeten nieuwe taken wachten, wat uiteindelijk time-outs veroorzaakt.

    ![HDInsight Job launcher queue view HDInsight Job launcher queue view HDInsight Job launcher queue view HDInsight](./media/hdinsight-troubleshoot-failed-cluster/hdi-job-launcher-queue.png)

    Er zijn twee manieren om dit probleem op te lossen: verlaag de snelheid van het inzenden van nieuwe taken of verhoog de verbruikssnelheid van oude taken door het cluster op te schalen.

2. GARENverwerking kan lang duren, wat time-outs kan veroorzaken.

    * Alle taken weergeven: dit is een tijdrovend gesprek. Deze oproep somt de toepassingen op van de YARN ResourceManager en krijgt voor elke voltooide toepassing de status van de YARN JobHistoryServer. Bij hogere aantallen taken kan deze oproep een time-out krijgen.

    * Taken aanbieden die ouder zijn dan zeven dagen: de HDInsight YARN JobHistoryServer is geconfigureerd om voltooide taakgegevens zeven dagen (waarde)`mapreduce.jobhistory.max-age-ms` te bewaren. Proberen om gezuiverde taken op te sommen resulteert in een time-out.

Ga als volgende te werk om deze problemen te diagnosticeren:

1. Het UTC-tijdsbereik bepalen om problemen op te lossen
2. Het juiste `webhcat.log` bestand(en) selecteren
3. Zoek naar WAARSCHUWING- en FOUTMELDINGen in die periode

#### <a name="other-webhcat-failures"></a>Andere WebHCat-fouten

1. HTTP-statuscode 500

    In de meeste gevallen waarin WebHCat 500 retourneert, bevat het foutbericht details over de fout. Anders, kijk `webhcat.log` door voor WAARSCHUWING en FOUTMELDINGen.

2. Taakfouten

    Er kunnen gevallen zijn waarin interacties met WebHCat succesvol zijn, maar de taken mislukken.

    Templeton verzamelt de taakconsole-uitvoer zoals `stderr` in `statusdir`, wat vaak handig is voor het oplossen van problemen. `stderr`bevat de YARN-toepassings-id van de werkelijke query.

## <a name="step-4-review-the-environment-stack-and-versions"></a>Stap 4: Bekijk de omgevingsstack en -versies

De pagina Ambari UI **Stack en Versie** bevat informatie over de configuratie van clusterservices en de geschiedenis van de serviceversie.  Onjuiste Hadoop-servicebibliotheekversies kunnen een oorzaak zijn van clusterfouten.  Selecteer in de gebruikersinterface van Ambari het menu **Beheerder** en vervolgens **Stapels en versies**.  Selecteer het tabblad **Versies** op de pagina om de versiegegevens van de service te bekijken:

![Apache Ambari Stack en versies](./media/hdinsight-troubleshoot-failed-cluster/ambari-stack-versions.png)

## <a name="step-5-examine-the-log-files"></a>Stap 5: De logboekbestanden onderzoeken

Er zijn veel soorten logboeken die worden gegenereerd uit de vele services en componenten die bestaan uit een HDInsight-cluster. [WebHCat logbestanden](#check-your-webhcat-service) zijn eerder beschreven. Er zijn verschillende andere nuttige logboekbestanden die u onderzoeken om problemen met uw cluster te beperken, zoals beschreven in de volgende secties.

* HDInsight-clusters bestaan uit verschillende knooppunten, waarvan de meeste de opdracht hebben om ingediende taken uit te voeren. Taken worden gelijktijdig uitgevoerd, maar logboekbestanden kunnen alleen resultaten lineair weergeven. HDInsight voert nieuwe taken uit en beëindigt anderen die niet als eerste worden voltooid. Al deze activiteiten worden `stderr` `syslog` geregistreerd op de bestanden en bestanden.

* De scriptactielogboekbestanden tonen fouten of onverwachte configuratiewijzigingen tijdens het creatieproces van uw cluster.

* De Hadoop stap logs identificeren Hadoop banen gelanceerd als onderdeel van een stap met fouten.

### <a name="check-the-script-action-logs"></a>De scriptactielogboeken controleren

[HdInsight-scriptacties](hdinsight-hadoop-customize-cluster-linux.md) voeren scripts op het cluster handmatig of wanneer opgegeven uit. Scriptacties kunnen bijvoorbeeld worden gebruikt om extra software op het cluster te installeren of om configuratie-instellingen te wijzigen vanaf de standaardwaarden. Als u de scriptactielogboeken controleert, kan dit inzicht geven in fouten die zijn opgetreden tijdens het instellen en configureren van het cluster.  U de status van een scriptactie bekijken door de knop **Ops** in de Ambari-gebruikersinterface te selecteren of door de logboeken te openen vanuit het standaardopslagaccount.

De scriptactielogboeken `\STORAGE_ACCOUNT_NAME\DEFAULT_CONTAINER_NAME\custom-scriptaction-logs\CLUSTER_NAME\DATE` bevinden zich in de map.

### <a name="view-hdinsight-logs-using-ambari-quick-links"></a>HDInsight-logboeken weergeven met Ambari Quick Links

De HDInsight Ambari UI bevat een aantal **Quick Links-secties.**  Als u de logboekkoppelingen voor een bepaalde service in uw HDInsight-cluster wilt openen, opent u de Ambari-gebruikersinterface voor uw cluster en selecteert u de servicekoppeling in de lijst aan de linkerkant. Selecteer de vervolgkeuzelijst **Snelle koppelingen,** vervolgens het HDInsight-knooppunt en selecteer vervolgens de koppeling voor het bijbehorende logboek.

Bijvoorbeeld voor HDFS-logboeken:

![Ambari Snelle links naar logboekbestanden](./media/hdinsight-troubleshoot-failed-cluster/apache-ambari-quick-links.png)

### <a name="view-hadoop-generated-log-files"></a>Hadoop-gegenereerde logboekbestanden weergeven

Een HDInsight-cluster genereert logboeken die zijn geschreven naar Azure-tabellen en Azure Blob-opslag. YARN maakt zijn eigen execution logs. Zie [Logboeken voor een HDInsight-cluster beheren voor](hdinsight-log-management.md#access-the-hadoop-log-files)meer informatie.

### <a name="review-heap-dumps"></a>Heap-dumps bekijken

Heapdumps bevatten een momentopname van het geheugen van de toepassing, inclusief de waarden van variabelen op dat moment, die handig zijn voor het diagnosticeren van problemen die optreden tijdens runtime. Zie [Heapdumps inschakelen voor Apache Hadoop-services op Linux-gebaseerde HDInsight voor](hdinsight-hadoop-collect-debug-heap-dump-linux.md)meer informatie.

## <a name="step-6-check-configuration-settings"></a>Stap 6: Configuratie-instellingen controleren

HDInsight-clusters zijn vooraf geconfigureerd met standaardinstellingen voor gerelateerde services, zoals Hadoop, Hive, HBase, enzovoort. Afhankelijk van het type cluster, de hardwareconfiguratie, het aantal knooppunten, de typen taken die u uitvoert en de gegevens waarmee u werkt (en hoe die gegevens worden verwerkt), moet u mogelijk uw configuratie optimaliseren.

Zie [Clusterconfiguraties optimaliseren met Apache Ambari](hdinsight-changing-configs-via-ambari.md)voor gedetailleerde instructies voor het optimaliseren van prestatieconfiguraties voor de meeste scenario's. Zie Apache [Spark-taken optimaliseren voor prestaties wanneer](spark/apache-spark-perf.md)u Spark gebruikt. 

## <a name="step-7-reproduce-the-failure-on-a-different-cluster"></a>Stap 7: De fout op een ander cluster reproduceren

Als u de bron van een clusterfout wilt diagnosticeren, start u een nieuw cluster met dezelfde configuratie en dient u de stappen van de mislukte taak één voor één opnieuw in. Controleer de resultaten van elke stap voordat u de volgende bewerking verwerkt. Deze methode geeft u de mogelijkheid om één mislukte stap te corrigeren en opnieuw uit te voeren. Deze methode heeft ook het voordeel dat u uw invoergegevens slechts één keer laadt.

1. Maak een nieuw testcluster met dezelfde configuratie als het mislukte cluster.
2. Verzend de eerste taakstap naar het testcluster.
3. Wanneer de bewerking is voltooid, controleert u op fouten in de staplogboekbestanden. Maak verbinding met het hoofdknooppunt van het testcluster en bekijk de logbestanden daar. De staplogboekbestanden worden pas weergegeven nadat de stap enige tijd is uitgevoerd, is voltooid of mislukt.
4. Als de eerste stap is geslaagd, voert u de volgende stap uit. Als er fouten zijn, onderzoekt u de fout in de logboekbestanden. Als er een fout in uw code is, voert u de correctie uit en voert u de stap opnieuw uit.
5. Ga door totdat alle stappen foutloos worden uitgevoerd.
6. Wanneer u klaar bent met het debuggen van het testcluster, verwijdert u het.

## <a name="next-steps"></a>Volgende stappen

* [HDInsight-clusters beheren met behulp van de Apache Ambari-webinterface](hdinsight-hadoop-manage-ambari.md)
* [HDInsight-logboeken analyseren](hdinsight-debug-jobs.md)
* [Toegang Apache Hadoop YARN applicatie teken in Linux-gebaseerde HDInsight](hdinsight-hadoop-access-yarn-app-logs-linux.md)
* [Heap dumps inschakelen voor Apache Hadoop services op Linux-gebaseerde HDInsight](hdinsight-hadoop-collect-debug-heap-dump-linux.md)
* [Bekende problemen voor Apache Spark-cluster op HDInsight](hdinsight-apache-spark-known-issues.md)
