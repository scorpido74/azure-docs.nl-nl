---
title: Clusters in HDInsight instellen met Apache Hadoop, Apache Spark, Apache Kafka en meer
description: Hadoop-, Kafka-, Spark-, HBase-, R-server- of Stormclusters instellen voor HDInsight vanuit een browser, de Azure-klassieker CLI, Azure PowerShell, REST of SDK.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.custom: hdinsightactive,hdiseo17may2017,seodec18
ms.date: 02/12/2020
ms.openlocfilehash: 246ec08e9b4edb33fa49318b68cc4364534282b9
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80064653"
---
# <a name="set-up-clusters-in-hdinsight-with-apache-hadoop-apache-spark-apache-kafka-and-more"></a>Clusters in HDInsight instellen met Apache Hadoop, Apache Spark, Apache Kafka en meer

[!INCLUDE [selector](../../includes/hdinsight-create-linux-cluster-selector.md)]

Meer informatie over het instellen en configureren van Apache Hadoop, Apache Spark, Apache Kafka, Interactive Query, Apache HBase, ML Services of Apache Storm in HDInsight. Lees ook hoe u clusters aanpassen en beveiliging toevoegen door ze aan een domein toe te voegen.

Een Hadoop-cluster bestaat uit verschillende virtuele machines (knooppunten) die worden gebruikt voor gedistribueerde verwerking van taken. Azure HDInsight verwerkt implementatiedetails van de installatie en configuratie van afzonderlijke knooppunten, zodat u alleen algemene configuratiegegevens hoeft te verstrekken.

> [!IMPORTANT]  
> De facturering voor het gebruik van HDInsight-clusters begint zodra er een cluster is gemaakt en stopt als een cluster wordt verwijderd. De facturering wordt pro-rato per minuut berekend, dus u moet altijd uw cluster verwijderen wanneer het niet meer wordt gebruikt. Meer informatie over het [verwijderen van een cluster.](hdinsight-delete-cluster.md)

## <a name="cluster-setup-methods"></a>Clusterinstallatiemethoden

In de volgende tabel ziet u de verschillende methoden die u gebruiken om een HDInsight-cluster in te stellen.

| Clusters gemaakt met | Webbrowser | Opdrachtregel | REST API | SDK |
| --- |:---:|:---:|:---:|:---:|
| [Azure-portal](hdinsight-hadoop-create-linux-clusters-portal.md) |✔ |&nbsp; |&nbsp; |&nbsp; |
| [Azure-gegevensfabriek](hdinsight-hadoop-create-linux-clusters-adf.md) |✔ |✔ |✔ |✔ |
| [Azure-CLI](hdinsight-hadoop-create-linux-clusters-azure-cli.md) |&nbsp; |✔ |&nbsp; |&nbsp; |
| [Azure PowerShell](hdinsight-hadoop-create-linux-clusters-azure-powershell.md) |&nbsp; |✔ |&nbsp; |&nbsp; |
| [Curl](hdinsight-hadoop-create-linux-clusters-curl-rest.md) |&nbsp; |✔ |✔ |&nbsp; |
| [Azure Resource Manager-sjablonen](hdinsight-hadoop-create-linux-clusters-arm-templates.md) |&nbsp; |✔ |&nbsp; |&nbsp; |

In dit artikel u de installatie in de [Azure-portal](https://portal.azure.com)doorlopen, waar u een HDInsight-cluster maken.

## <a name="basics"></a>Basisbeginselen

![hdinsight maken opties op maat snel](./media/hdinsight-hadoop-provision-linux-clusters/azure-portal-cluster-basics-blank-fs.png)

### <a name="project-details"></a>Projectdetails

[Azure Resource Manager](../azure-resource-manager/management/overview.md) helpt u met het werken met de resources in uw toepassing als een groep, aangeduid als een [Azure-brongroep.](../azure-resource-manager/management/overview.md#resource-groups) U alle bronnen voor uw toepassing implementeren, bijwerken, controleren of verwijderen in één gecoördineerde bewerking.

### <a name="cluster-details"></a>Clusterdetails

#### <a name="cluster-name"></a>Clusternaam

HDInsight-clusternamen hebben de volgende beperkingen:

* Toegestane tekens: a-z, 0-9, A-Z
* Maximale lengte: 59
* Gereserveerde namen: apps
* De clusternaamgevingsscope is voor alle Azure's, voor alle abonnementen. De clusternaam moet dus wereldwijd uniek zijn.
* De eerste zes tekens moeten uniek zijn binnen een virtueel netwerk

#### <a name="region"></a>Regio

U hoeft de clusterlocatie niet expliciet op te geven: het cluster bevindt zich op dezelfde locatie als de standaardopslag. Selecteer de vervolgkeuzelijst **Regio** op [HDInsight-prijzen](https://go.microsoft.com/fwLink/?LinkID=282635&clcid=0x409)voor een lijst met ondersteunde regio's .

#### <a name="cluster-type"></a>Clustertype

Azure HDInsight biedt momenteel de volgende clustertypen, elk met een set componenten om bepaalde functionaliteiten te bieden.

> [!IMPORTANT]  
> HDInsight clusters zijn beschikbaar in verschillende types, elk voor één workload of technologie. Er is geen ondersteunde methode om een cluster te maken dat meerdere typen combineert, zoals Storm en HBase op één cluster. Als uw oplossing technologieën vereist die zijn verspreid over meerdere HDInsight-clustertypen, kan een [virtueel Azure-netwerk](https://docs.microsoft.com/azure/virtual-network) de vereiste clustertypen verbinden.

| Clustertype | Functionaliteit |
| --- | --- |
| [Hadoop](hadoop/apache-hadoop-introduction.md) |Batchquery en analyse van opgeslagen gegevens |
| [HBase](hbase/apache-hbase-overview.md) |Verwerking voor grote hoeveelheden schemaloze NoSQL-gegevens |
| [Interactive Query](./interactive-query/apache-interactive-query-get-started.md) |In-memory caching voor interactieve en snellere Hive-query's |
| [Kafka](kafka/apache-kafka-introduction.md) | Een gedistribueerd streamingplatform dat kan worden gebruikt om realtime streaming datapipelines en -toepassingen te bouwen |
| [ML-services](r-server/r-server-overview.md) |Diverse big data-statistieken, voorspellende modellering en machine learning-mogelijkheden |
| [Vonk](spark/apache-spark-overview.md) |Verwerking in het geheugen, interactieve query's, verwerking van microbatchstromen |
| [Storm](storm/apache-storm-overview.md) |Gebeurtenissen in realtime verwerken |

#### <a name="version"></a>Versie

Kies de versie van HDInsight voor dit cluster. Zie [Ondersteunde HDInsight-versies](hdinsight-component-versioning.md#supported-hdinsight-versions)voor meer informatie.

### <a name="cluster-credentials"></a>Clusterreferenties

Met HDInsight-clusters u twee gebruikersaccounts configureren tijdens het maken van clusters:

* Gebruikersnaam voor inloggen van het cluster: de standaardgebruikersnaam is *beheerder.* Het maakt gebruik van de basisconfiguratie op de Azure-portal. Soms heet het 'Clustergebruiker' of 'HTTP-gebruiker'.
* Secure Shell (SSH) gebruikersnaam: wordt gebruikt om verbinding te maken met het cluster via SSH. Zie [SSH gebruiken met HDInsight](hdinsight-hadoop-linux-use-ssh-unix.md)voor meer informatie.

De HTTP-gebruikersnaam heeft de volgende beperkingen:

* Toegestane speciale tekens: `_` en`@`
* Tekens niet toegestaan: #;;',,,,,,:'!*??$()\/{}[]<>|&--=+%~^ruimte
* Maximale lengte: 20

De SSH-gebruikersnaam heeft de volgende beperkingen:

* Toegestane speciale tekens:`_` en`@`
* Tekens niet toegestaan: #;;',,,,,,:'!*??$()\/{}[]<>|&--=+%~^ruimte
* Maximale lengte: 64
* Gereserveerde namen: hadoop, gebruikers, oozie, hive, mapred, ambari-qa, zookeeper, tez, hdfs, sqoop, garen, hcat, ams, hbase, storm, beheerder, admin, gebruiker, gebruiker1, test, user2, test1, user3, admin1, 1, 123, a, actuser, adm, admin2, aspnet, back-up, console, david, gast, john, eigenaar, root, server, sql, ondersteuning, support_388945a0, sys, test2, test3, user4, user5, spark

## <a name="storage"></a>Storage

![Clusteropslaginstellingen: HDFS-compatibele eindpunten](./media/hdinsight-hadoop-provision-linux-clusters/azure-portal-cluster-storage.png)

Hoewel een on-premises installatie van Hadoop het Hadoop Distributed File System (HDFS) gebruikt voor opslag op het cluster, gebruikt u in de cloud opslageindpunten die zijn verbonden met het cluster. Met behulp van cloudopslag u de HDInsight-clusters die worden gebruikt voor berekening veilig verwijderen terwijl u uw gegevens behoudt.

HDInsight-clusters kunnen de volgende opslagopties gebruiken:

* Azure Data Lake Storage Gen2
* Azure Data Lake Storage Gen1
* Azure Storage General Purpose v2
* Azure Storage General Purpose v1
* Blob azure storage block **(alleen ondersteund als secundaire opslag)**

Zie [Opslagopties vergelijken voor gebruik met Azure HDInsight-clusters voor](hdinsight-hadoop-compare-storage-options.md)meer informatie over opslagopties met HDInsight.

> [!WARNING]  
> Het gebruik van een extra opslagaccount op een andere locatie dan het HDInsight-cluster wordt niet ondersteund.

Tijdens de configuratie geeft u voor het standaardopslageindpunt een blobcontainer op van een Azure Storage-account of Data Lake Storage. De standaardopslag bevat toepassings- en systeemlogboeken. Optioneel u aanvullende gekoppelde Azure Storage-accounts en Data Lake Storage-accounts opgeven waartoe het cluster toegang heeft. Het HDInsight-cluster en de afhankelijke opslagaccounts moeten zich op dezelfde Azure-locatie bevinden.

[!INCLUDE [secure-transfer-enabled-storage-account](../../includes/hdinsight-secure-transfer.md)]

### <a name="metastore-settings"></a>Metastore-instellingen

U optionele Hive- of Apache Oozie-metastores maken. Niet alle clustertypen ondersteunen echter metastores en Azure SQL Data Warehouse is niet compatibel met metastores.

Zie [Externe metagegevensopslag gebruiken in Azure HDInsight](./hdinsight-use-external-metadata-stores.md)voor meer informatie.

> [!IMPORTANT]  
> Wanneer u een aangepaste metastore maakt, gebruikt u geen streepjes, koppeltekens of spaties in de databasenaam. Hierdoor kan het proces voor het maken van het clusterproces mislukken.

#### <a name="sql-database-for-hive"></a>SQL-database voor Hive

Als u uw Hive-tabellen wilt behouden nadat u een HDInsight-cluster hebt verwijderd, gebruikt u een aangepaste metastore. U de metastore vervolgens koppelen aan een ander HDInsight-cluster.

Een HDInsight-metastore die is gemaakt voor één HDInsight-clusterversie, kan niet worden gedeeld via verschillende HDInsight-clusterversies. Zie [Ondersteunde HDInsight-versies](hdinsight-component-versioning.md#supported-hdinsight-versions)voor een lijst met HDInsight-versies.

#### <a name="sql-database-for-oozie"></a>SQL-database voor Oozie

Gebruik een aangepaste metastore om de prestaties te verhogen bij het gebruik van Oozie. Een metastore kan ook toegang bieden tot oozie-taakgegevens nadat u uw cluster hebt verwijderd.

#### <a name="sql-database-for-ambari"></a>SQL-database voor Ambari

Ambari wordt gebruikt om HDInsight-clusters te bewaken, configuratiewijzigingen aan te brengen en clusterbeheergegevens en taakgeschiedenis op te slaan. Met de aangepaste Ambari DB-functie u een nieuw cluster implementeren en Ambari instellen in een externe database die u beheert. Zie [Custom Ambari DB](./hdinsight-custom-ambari-db.md)voor meer informatie.

> [!IMPORTANT]  
> U een aangepaste Oozie-metastore niet opnieuw gebruiken. Als u een aangepaste Oozie-metastore wilt gebruiken, moet u een lege Azure SQL-database opgeven bij het maken van het HDInsight-cluster.

## <a name="security--networking"></a>Beveiliging + netwerken

![hdinsight maken opties kiezen enterprise security pakket](./media/hdinsight-hadoop-provision-linux-clusters/azure-portal-cluster-security-networking.png)

### <a name="enterprise-security-package"></a>Bedrijfsbeveiligingspakket

Voor clustertypen Hadoop, Spark, HBase, Kafka en Interactieve Query u ervoor kiezen om het **Enterprise Security Package**in te schakelen. Dit pakket biedt de mogelijkheid om een veiliger clustersetup te hebben door Apache Ranger te gebruiken en te integreren met Azure Active Directory. Zie [Overzicht van bedrijfsbeveiliging in Azure HDInsight](./domain-joined/hdinsight-security-overview.md)voor meer informatie.

Met het Enterprise-beveiligingspakket u HDInsight integreren met Active Directory en Apache Ranger. Er kunnen meerdere gebruikers worden gemaakt met behulp van het Enterprise-beveiligingspakket.

Zie [Sandbox-omgeving voor domeinverbonden HDInsight voor](./domain-joined/apache-domain-joined-configure.md)meer informatie over het maken van een HDInsight-cluster dat is verbonden met een domein.

### <a name="tls"></a>TLS

Zie [Transport Layer Security](./hdinsight-plan-virtual-network-deployment.md#transport-layer-security) voor meer informatie

### <a name="virtual-network"></a>Virtueel netwerk

Als uw oplossing technologieën vereist die zijn verspreid over meerdere HDInsight-clustertypen, kan een [virtueel Azure-netwerk](https://docs.microsoft.com/azure/virtual-network) de vereiste clustertypen verbinden. Met deze configuratie kunnen de clusters en alle code die u aan hen implementeert, rechtstreeks met elkaar communiceren.

Zie [Een virtueel netwerk voor HDInsight plannen voor](hdinsight-plan-virtual-network-deployment.md)meer informatie over het gebruik van een virtueel Azure-netwerk met HDInsight.

Zie Gestructureerde streaming van [Apache Spark gebruiken met Apache Kafka](hdinsight-apache-kafka-spark-structured-streaming.md)voor een voorbeeld van het gebruik van twee clustertypen binnen een virtueel Azure-netwerk. Zie [Een virtueel netwerk voor HDInsight plannen voor](hdinsight-plan-virtual-network-deployment.md)meer informatie over het gebruik van HDInsight met een virtueel netwerk, inclusief specifieke configuratievereisten voor het virtuele netwerk.

### <a name="disk-encryption-setting"></a>Instelling voor schijfversleuteling

Zie [Door de klant beheerde sleutelschijfversleuteling](./disk-encryption.md)voor meer informatie.

### <a name="kafka-rest-proxy"></a>Kafka REST-proxy

Deze instelling is alleen beschikbaar voor clustertype Kafka. Zie [Een REST-proxy gebruiken](./kafka/rest-proxy.md)voor meer informatie.

### <a name="identity"></a>Identiteit

Zie [Beheerde identiteiten in Azure HDInsight](./hdinsight-managed-identities.md)voor meer informatie.

## <a name="configuration--pricing"></a>Configuratie + prijzen

![HDInsight kiest uw knooppuntgrootte](./media/hdinsight-hadoop-provision-linux-clusters/azure-portal-cluster-configuration.png)

Er worden kosten in rekening gebracht voor knooppuntgebruik zolang het cluster bestaat. Facturering begint wanneer een cluster wordt gemaakt en stopt wanneer het cluster wordt verwijderd. Clusters kunnen niet worden verwijderd of in de wacht worden gezet.

### <a name="node-configuration"></a>Knooppuntconfiguratie

Elk clustertype heeft zijn eigen aantal knooppunten, terminologie voor knooppunten en standaardVM-grootte. In de volgende tabel is het aantal knooppunten voor elk knooppunttype tussen haakjes.

| Type | Knooppunten | Diagram |
| --- | --- | --- |
| Hadoop |Hoofdknooppunt (2), Worker node (1+) |![HDInsight Hadoop-clusterknooppunten](./media/hdinsight-hadoop-provision-linux-clusters/hdinsight-hadoop-cluster-type-nodes.png) |
| HBase |Hoofdserver (2), regioserver (1+), master/ZooKeeper-knooppunt (3) |![HDInsight HBase-clustertype-instelling](./media/hdinsight-hadoop-provision-linux-clusters/hdinsight-hbase-cluster-type-setup.png) |
| Storm |Nimbus node (2), supervisor server (1+), ZooKeeper node (3) |![HDInsight-installatie van het clustertype van het stormcluster](./media/hdinsight-hadoop-provision-linux-clusters/hdinsight-storm-cluster-type-setup.png) |
| Spark |Hoofdknooppunt (2), Worker node (1+), ZooKeeper node (3) (gratis voor A1 ZooKeeper VM-formaat) |![HDInsight-configuratie van sparkclustertypen](./media/hdinsight-hadoop-provision-linux-clusters/hdinsight-spark-cluster-type-setup.png) |

Zie [Standaardknooppuntconfiguratie en virtuele machineformaten voor clusters voor meer](hdinsight-component-versioning.md#default-node-configuration-and-virtual-machine-sizes-for-clusters) informatie in 'Wat zijn de Hadoop-componenten en -versies in HDInsight?'

De kosten van HDInsight-clusters worden bepaald door het aantal knooppunten en de virtuele machineformaten voor de knooppunten.

Verschillende clustertypen hebben verschillende knooppunttypen, aantallen knooppunten en knooppuntgroottes:
* Hadoop-clustertype standaard:
    * Twee *hoofdknooppunten*  
    * Vier *worker-knooppunten*
* Standaard clustertype storm:
    * Twee *Nimbus-knooppunten*
    * Drie *ZooKeeper knooppunten*
    * Vier *supervisorknooppunten*

Als u HDInsight gewoon uitprobeert, raden we u aan één Worker-knooppunt te gebruiken. Zie [HDInsight-prijzen](https://go.microsoft.com/fwLink/?LinkID=282635&clcid=0x409)voor meer informatie over HDInsight-prijzen.

> [!NOTE]  
> De limiet voor de clustergrootte varieert tussen Azure-abonnementen. Neem contact op met [Azure-factureringsondersteuning](https://docs.microsoft.com/azure/azure-portal/supportability/how-to-create-azure-support-request) om de limiet te verhogen.

Wanneer u de Azure-portal gebruikt om het cluster te configureren, is de grootte van het knooppunt beschikbaar via het tabblad **Configuratie + prijzen.** In de portal u ook de kosten zien die verbonden zijn aan de verschillende knooppuntgroottes.

### <a name="virtual-machine-sizes"></a>Grootten van virtuele machines

Wanneer u clusters implementeert, kiest u rekenresources op basis van de oplossing die u wilt implementeren. De volgende VM's worden gebruikt voor HDInsight-clusters:

* Vm's uit de A- en D1-4-serie: [Linux VM-formaten voor algemene doeleinden](https://docs.microsoft.com/azure/virtual-machines/linux/sizes-general)
* D11-14 serie VM: [Geheugengeoptimaliseerde Linux VM-formaten](https://docs.microsoft.com/azure/virtual-machines/linux/sizes-memory)

Zie [VM-formaten gebruiken voor HDInsight-clusters](../cloud-services/cloud-services-sizes-specs.md#size-tables)voor het opgeven van een vm-grootte als u wilt weten welke waarde u moet gebruiken om een VM-grootte op te geven tijdens het maken van een cluster met de verschillende SDK's of tijdens het gebruik van Azure PowerShell. Gebruik in dit gekoppelde artikel de waarde in de kolom **Grootte** van de tabellen.

> [!IMPORTANT]  
> Als u meer dan 32 Worker-knooppunten in een cluster nodig hebt, moet u een hoofdknooppuntgrootte selecteren met ten minste 8 cores en 14 GB RAM.

Zie [Grootte voor virtuele machines voor](../virtual-machines/windows/sizes.md)meer informatie . Zie [HDInsight-prijzen](https://azure.microsoft.com/pricing/details/hdinsight)voor informatie over de prijzen van de verschillende formaten.

### <a name="add-application"></a>Toepassing toevoegen

Een HDInsight-toepassing is een toepassing die gebruikers kunnen installeren op een op Linux gebaseerd HDInsight-cluster. U toepassingen van Microsoft, derden of die u zelf ontwikkelt, gebruiken. Zie [Apache Hadoop-toepassingen van derden installeren op Azure HDInsight](hdinsight-apps-install-applications.md)voor meer informatie.

De meeste HDInsight-toepassingen zijn geïnstalleerd op een leeg randknooppunt.  Een leeg randknooppunt is een Linux virtuele machine met dezelfde client tools geïnstalleerd en geconfigureerd als in het hoofdknooppunt. U het randknooppunt gebruiken voor toegang tot het cluster, het testen van uw clienttoepassingen en het hosten van uw clienttoepassingen. Zie [Lege randknooppunten gebruiken in HDInsight](hdinsight-apps-use-edge-node.md)voor meer informatie.

### <a name="script-actions"></a>Scriptacties

U extra componenten installeren of clusterconfiguratie aanpassen met behulp van scripts tijdens het maken. Dergelijke scripts worden aangeroepen via **Script Action**, een configuratieoptie die kan worden gebruikt vanuit de Azure-portal, HDInsight Windows PowerShell-cmdlets of de HDInsight .NET SDK. Zie [HDInsight-cluster aanpassen met scriptactie](hdinsight-hadoop-customize-cluster-linux.md)voor meer informatie.

Sommige native Java-componenten, zoals Apache Mahout en Cascading, kunnen worden uitgevoerd op het cluster als Java Archive (JAR) bestanden. Deze JAR-bestanden kunnen worden gedistribueerd naar Azure Storage en worden verzonden naar HDInsight-clusters met hadoop-mechanismen voor taakindiening. Zie [Apache Hadoop-vacatures programmatisch inzenden](hadoop/submit-apache-hadoop-jobs-programmatically.md)voor meer informatie.

> [!NOTE]  
> Als u problemen hebt met het implementeren van JAR-bestanden in HDInsight-clusters of het aanroepen van JAR-bestanden op HDInsight-clusters, neemt u contact op met [Microsoft Support.](https://azure.microsoft.com/support/options/)
>
> Cascading wordt niet ondersteund door HDInsight en komt niet in aanmerking voor Microsoft Support. Zie Nieuw in de [clusterversies](hdinsight-component-versioning.md)van HDInsight voor lijsten met ondersteunde componenten.

Soms wilt u de volgende configuratiebestanden configureren tijdens het creatieproces:

* clusterIdentiteit.xml
* core-site.xml
* gateway.xml
* hbase-env.xml
* hbase-site.xml
* hdfs-site.xml
* hive-env.xml
* hive-site.xml
* mapred-site
* oozie-site.xml
* oozie-env.xml
* stormsite.xml
* tez-site.xml
* webhcat-site.xml
* garen-site.xml

Zie [CLUSTERS van HDInsight aanpassen met Bootstrap](hdinsight-hadoop-customize-cluster-bootstrap.md)voor meer informatie.

## <a name="next-steps"></a>Volgende stappen

* [Fouten bij het maken van groepen oplossen met Azure HDInsight](./hadoop/hdinsight-troubleshoot-cluster-creation-fails.md)
* [Wat zijn HDInsight, het Apache Hadoop ecosysteem en Hadoop clusters?](hadoop/apache-hadoop-introduction.md)
* [Aan de slag met Apache Hadoop in HDInsight](hadoop/apache-hadoop-linux-tutorial-get-started.md)
* [Werken in Apache Hadoop op HDInsight vanaf een Windows-pc](hdinsight-hadoop-windows-tools.md)
