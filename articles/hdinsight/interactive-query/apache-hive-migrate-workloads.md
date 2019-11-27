---
title: Azure HDInsight 3,6 Hive-workloads migreren naar HDInsight 4,0
description: Meer informatie over het migreren van Apache Hive-workloads op HDInsight 3,6 naar HDInsight 4,0.
author: msft-tacox
ms.author: tacox
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.date: 11/13/2019
ms.openlocfilehash: 3d55e0e7ecbd52b6d96c657e333c5557388f2721
ms.sourcegitcommit: dd0304e3a17ab36e02cf9148d5fe22deaac18118
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/22/2019
ms.locfileid: "74406512"
---
# <a name="migrate-azure-hdinsight-36-hive-workloads-to-hdinsight-40"></a>Azure HDInsight 3,6 Hive-workloads migreren naar HDInsight 4,0

Dit document bevat informatie over het migreren van Apache Hive-en LLAP-workloads op HDInsight 3,6 naar HDInsight 4,0. HDInsight 4,0 biedt nieuwere Hive-en LLAP-functies, zoals gerealiseerde weer gaven en caches voor query resultaten. Wanneer u uw workloads migreert naar HDInsight 4,0, kunt u veel nieuwere functies van Hive 3 gebruiken die niet beschikbaar zijn op HDInsight 3,6.

In dit artikel komen de volgende onderwerpen aan bod:

* Migratie van Hive-meta gegevens naar HDInsight 4,0
* Veilige migratie van zuren en niet-zure tabellen
* Behoud van Hive-beveiligings beleid in HDInsight-versies
* Uitvoering van query's en fout opsporing van HDInsight 3,6 naar HDInsight 4,0

Een voor deel van het onderdeel is de mogelijkheid om meta gegevens te exporteren naar een externe data base (waarnaar wordt verwezen als het Hive-archief). De **Hive-meta Store** is verantwoordelijk voor het opslaan van tabel statistieken, met inbegrip van de tabel opslag locatie, kolom namen en tabel index informatie. Het schema van de meta store-data base wijkt af van de Hive-versies. De aanbevolen manier om de Hive-metastore veilig bij te werken is het maken van een kopie en het bijwerken van de kopie in plaats van de huidige productie omgeving.

## <a name="copy-metastore"></a>Meta Store kopiëren

Voor HDInsight 3,6 en HDInsight 4,0 zijn verschillende meta Store-schema's vereist en er kan geen enkele meta Store worden gedeeld.

### <a name="external-metastore"></a>Externe meta Store

Maak een nieuwe kopie van uw externe meta Store. Als u een externe meta Store gebruikt, is een van de veilige en eenvoudige manieren om een kopie van de meta Store te maken, [het herstellen van de data base](../../sql-database/sql-database-recovery-using-backups.md#point-in-time-restore) met een andere naam met behulp van de functie voor het terugzetten van SQL database.  Zie [externe meta gegevensopslag plaatsen in azure HDInsight gebruiken](../hdinsight-use-external-metadata-stores.md) voor meer informatie over het koppelen van een externe metadata voor een HDInsight-cluster.

### <a name="internal-metastore"></a>Interne meta Store

Als u de interne meta Store gebruikt, kunt u query's gebruiken om object definities in de Hive-metastore te exporteren en deze in een nieuwe Data Base te importeren.

1. Maak verbinding met het HDInsight-cluster met behulp van een [SSH-client (Secure Shell)](../hdinsight-hadoop-linux-use-ssh-unix.md).

1. Maak verbinding met HiveServer2 met uw [Beeline-client](../hadoop/apache-hadoop-use-hive-beeline.md) vanuit uw open SSH-sessie door de volgende opdracht in te voeren:

    ```hiveql
    for d in `beeline -u "jdbc:hive2://localhost:10001/;transportMode=http" --showHeader=false --silent=true --outputformat=tsv2 -e "show databases;"`; do echo "create database $d; use $d;" >> alltables.sql; for t in `beeline -u "jdbc:hive2://localhost:10001/$d;transportMode=http" --showHeader=false --silent=true --outputformat=tsv2 -e "show tables;"` ; do ddl=`beeline -u "jdbc:hive2://localhost:10001/$d;transportMode=http" --showHeader=false --silent=true --outputformat=tsv2 -e "show create table $t;"`; echo "$ddl ;" >> alltables.sql ; echo "$ddl" | grep -q "PARTITIONED\s*BY" && echo "MSCK REPAIR TABLE $t ;" >> alltables.sql ; done; done
    ```

    Met deze opdracht wordt een bestand met de naam **AllTables. SQL**gegenereerd. Omdat de standaard database niet kan worden verwijderd/opnieuw moet worden gemaakt, verwijdert u de `create database default;`-instructie in **AllTables. SQL**.

1. Sluit uw SSH-sessie af. Voer vervolgens een SCP-opdracht in om **AllTables. SQL** lokaal te downloaden.

    ```bash
    scp sshuser@CLUSTERNAME-ssh.azurehdinsight.net:alltables.sql c:/hdi
    ```

1. Upload **AllTables. SQL** naar het *nieuwe* HDInsight-cluster.

    ```bash
    scp c:/hdi/alltables.sql sshuser@CLUSTERNAME-ssh.azurehdinsight.net:/home/sshuser/
    ```

1. Gebruik vervolgens SSH om verbinding te maken met het *nieuwe* HDInsight-cluster. Voer de volgende code uit vanuit de SSH-sessie:

    ```bash
    beeline -u "jdbc:hive2://localhost:10001/;transportMode=http" -i alltables.sql
    ```

## <a name="upgrade-metastore"></a>Meta Store bijwerken

Zodra de meta Store- **kopie** is voltooid, voert u een schema-upgrade script uit in [script actie](../hdinsight-hadoop-customize-cluster-linux.md) op het bestaande HDInsight 3,6-cluster om de nieuwe Meta Store naar Hive 3-schema bij te werken. Hierdoor kan de Data Base worden gekoppeld als HDInsight 4,0-meta Store.

Gebruik de waarden in de tabel hieronder. Vervang `SQLSERVERNAME DATABASENAME USERNAME PASSWORD` door de juiste waarden voor de **gekopieerde** Hive-metastore, gescheiden door spaties. Neem '. database.windows.net ' niet op bij het opgeven van de naam van de SQL-Server.

|Eigenschap | Waarde |
|---|---|
|Script type|-Aangepast|
|Naam|Hive-upgrade|
|Bash-script-URI|`https://hdiconfigactions.blob.core.windows.net/hivemetastoreschemaupgrade/launch-schema-upgrade.sh`|
|Knooppunt type (n)|Head|
|Parameters|GEBRUIKERS NAAM DATABASENAME DATA BASE WACHT WOORD|

> [!Warning]  
> De upgrade waarmee het meta gegevens schema van HDInsight 3,6 wordt geconverteerd naar het HDInsight 4,0-schema, kan niet worden omgekeerd.

U kunt de upgrade controleren door de volgende SQL-query uit te voeren op de Data Base:

```sql
select * from dbo.version
```

## <a name="migrate-hive-tables-to-hdinsight-40"></a>Hive-tabellen migreren naar HDInsight 4,0

Na het volt ooien van de vorige set stappen voor het migreren van de Hive-meta Store naar HDInsight 4,0, zijn de tabellen en data bases die zijn vastgelegd in de meta Store, zichtbaar vanuit het HDInsight 4,0-cluster door `show tables` of `show databases` vanuit het cluster uit te voeren. Zie [query's](#query-execution-across-hdinsight-versions) uitvoeren in hdinsight-versies voor meer informatie over het uitvoeren van Query's in hdinsight 4,0-clusters.

De werkelijke gegevens uit de tabellen zijn echter niet toegankelijk totdat het cluster toegang heeft tot de benodigde opslag accounts. Voer de volgende stappen uit om ervoor te zorgen dat uw HDInsight 4,0-cluster toegang heeft tot dezelfde gegevens als uw oude HDInsight 3,6-cluster:

1. Bepaal het Azure-opslag account van de tabel of Data Base.

1. Als uw HDInsight 4,0-cluster al wordt uitgevoerd, koppelt u het Azure Storage-account aan het cluster via Ambari. Als u het HDInsight 4,0-cluster nog niet hebt gemaakt, moet u ervoor zorgen dat het Azure Storage-account is opgegeven als het primaire of secundaire cluster opslag account. Zie [extra opslag accounts toevoegen aan hdinsight](../hdinsight-hadoop-add-storage.md)voor meer informatie over het toevoegen van opslag accounts aan hdinsight-clusters.

## <a name="deploy-new-hdinsight-40-and-connect-to-the-new-metastore"></a>Nieuwe HDInsight 4,0 implementeren en verbinding maken met de nieuwe Meta Store

Nadat de schema-upgrade is voltooid, implementeert u een nieuw HDInsight 4,0-cluster en verbindt u de bijgewerkte meta Store. Als u 4,0 al hebt geïmplementeerd, stelt u deze zo in dat u verbinding kunt maken met de meta Store vanuit Ambari.

## <a name="run-schema-migration-script-from-hdinsight-40"></a>Schema migratie script uitvoeren vanuit HDInsight 4,0

Tabellen worden anders behandeld in HDInsight 3,6 en HDInsight 4,0. Daarom kunt u niet dezelfde tabellen delen voor clusters van verschillende versies. Als u HDInsight 3,6 op hetzelfde moment als HDInsight 4,0 wilt gebruiken, moet u afzonderlijke exemplaren van de gegevens voor elke versie hebben.

Uw Hive-workload kan bestaan uit een combi natie van zuren en niet-zure tabellen. Een belang rijk verschil tussen Hive op HDInsight 3,6 (hive 2) en Hive op HDInsight 4,0 (Hive 3) is zuur bestendig voor tabellen. In HDInsight 3,6 is aanvullende configuratie vereist, maar in HDInsight 4,0-tabellen zijn standaard ACID-compatibel. De enige vereiste actie voor de migratie is het uitvoeren van een grote compressie op basis van de tabel zuur op het 3,6-cluster. Voer de volgende query uit vanuit de Hive-weer gave of vanuit beeline:

```sql
alter table myacidtable compact 'major';
```

Deze compressie is vereist omdat de tabellen HDInsight 3,6 en HDInsight 4,0 ACID een andere betekenis hebben. Door compressie wordt een schone pastel afgedwongen die consistentie waarborgt. Sectie 4 van de [documentatie voor Hive-migratie](https://docs.hortonworks.com/HDPDocuments/Ambari-2.7.3.0/bk_ambari-upgrade-major/content/prepare_hive_for_upgrade.html) bevat richt lijnen voor bulk compressie van HDINSIGHT 3,6 zuren-tabellen.

Wanneer u de migratie en de compressie stappen van de meta Store hebt voltooid, kunt u het werkelijke magazijn migreren. Nadat u de Hive-Warehouse migratie hebt voltooid, heeft het HDInsight 4,0-magazijn de volgende eigenschappen:

|3.6 |4.0 |
|---|---|
|Externe tabellen|Externe tabellen|
|Niet-transactionele beheerde tabellen|Externe tabellen|
|Transactionele beheerde tabellen|Beheerde tabellen|

Mogelijk moet u de eigenschappen van uw magazijn aanpassen voordat u de migratie uitvoert. Als u bijvoorbeeld verwacht dat een bepaalde tabel wordt gebruikt door een derde partij (zoals een HDInsight 3,6-cluster), moet deze tabel extern zijn zodra de migratie is voltooid. In HDInsight 4,0 zijn alle beheerde tabellen transactioneel. Beheerde tabellen in HDInsight 4,0 mogen daarom alleen worden gebruikt door HDInsight 4,0-clusters.

Zodra de tabel eigenschappen correct zijn ingesteld, voert u het hulp programma voor het migratie hulpprogramma voor het Hive-magazijn uit vanuit een van de cluster-hoofd knooppunten met behulp van de SSH-Shell:

1. Maak via SSH verbinding met uw cluster-hoofd knooppunt. Zie [verbinding maken met HDInsight via SSH](../hdinsight-hadoop-linux-use-ssh-unix.md) voor instructies.
1. Open een aanmeldings shell als de Hive-gebruiker door uit te voeren `sudo su - hive`
1. Bepaal de versie van de gegevens platform stack door `ls /usr/hdp`uit te voeren. Hiermee wordt een versie teken reeks weer gegeven die u moet gebruiken in de volgende opdracht.
1. Voer de volgende opdracht uit vanuit de shell. Vervang `STACK_VERSION` door de versie teken reeks uit de vorige stap:

```bash
/usr/hdp/STACK_VERSION/hive/bin/hive --config /etc/hive/conf --service  strictmanagedmigration --hiveconf hive.strict.managed.tables=true -m automatic --modifyManagedTables
```

Nadat het migratie hulpprogramma is voltooid, is het Hive-magazijn gereed voor HDInsight 4,0.

> [!Important]  
> Beheerde tabellen in HDInsight 4,0 (inclusief tabellen die zijn gemigreerd vanuit 3,6) mogen niet worden gebruikt door andere services of toepassingen, waaronder HDInsight 3,6-clusters.

## <a name="secure-hive-across-hdinsight-versions"></a>Component beveiligen in HDInsight-versies

Sinds HDInsight 3,6 wordt HDInsight geïntegreerd met Azure Active Directory met HDInsight Enterprise Security Package (ESP). ESP maakt gebruik van Kerberos en Apache zwerver voor het beheren van de machtigingen van specifieke bronnen in het cluster. Zwerver-beleid dat is geïmplementeerd op Hive in HDInsight 3,6 kan worden gemigreerd naar HDInsight 4,0 met de volgende stappen:

1. Navigeer naar het Service Manager paneel zwerver in uw HDInsight 3,6-cluster.
2. Navigeer naar het beleid met de naam **Hive** en exporteer het beleid naar een JSON-bestand.
3. Zorg ervoor dat alle gebruikers waarnaar wordt verwezen in de JSON van het geëxporteerde beleid aanwezig zijn in het nieuwe cluster. Als een gebruiker naar wordt verwezen in de JSON van het beleid, maar niet bestaat in het nieuwe cluster, voegt u de gebruiker toe aan het nieuwe cluster of verwijdert u de verwijzing van het beleid.
4. Navigeer naar het **Service Manager paneel zwerver** in uw HDInsight 4,0-cluster.
5. Navigeer naar het beleid met de naam **Hive** en importeer de JSON-beleids regel uit stap 2.

## <a name="check-compatibility-and-modify-codes-as-needed-in-test-app"></a>Controleer de compatibiliteit en Wijzig codes naar wens in de app testen

Bij het migreren van werk belastingen, zoals bestaande Program ma's en query's, raadpleegt u de opmerkingen bij de release en documentatie voor wijzigingen en past u waar nodig wijzigingen toe. Als uw HDInsight 3,6-cluster gebruikmaakt van een gedeelde Spark en Hive-metastore, is [aanvullende configuratie vereist met hive Warehouse connector](./apache-hive-warehouse-connector.md) .

## <a name="deploy-new-app-for-production"></a>Nieuwe app voor productie implementeren

Als u wilt overschakelen naar het nieuwe cluster, bijvoorbeeld door een nieuwe client toepassing te installeren en deze als een nieuwe productie omgeving te gebruiken, of u kunt uw bestaande client toepassing upgraden en overschakelen naar HDInsight 4,0.

## <a name="switch-hdinsight-40-to-the-production"></a>HDInsight 4,0 overschakelen naar de productie omgeving

Als er tijdens het testen verschillen zijn gemaakt in de meta Store, moet u de wijzigingen direct bijwerken voordat u overschakelt. In dit geval kunt u de meta Store exporteren & importeren en opnieuw upgraden.

## <a name="remove-the-old-production"></a>De oude productie verwijderen

Nadat u hebt bevestigd dat de release volledig en operationeel is, kunt u versie 3,6 en de vorige meta Store verwijderen. Zorg ervoor dat alles wordt gemigreerd voordat u de omgeving verwijdert.

## <a name="query-execution-across-hdinsight-versions"></a>Uitvoering van query's in HDInsight-versies

Er zijn twee manieren voor het uitvoeren en opsporen van fouten in Hive/LLAP-query's binnen een HDInsight 3,6-cluster. HiveCLI biedt een opdracht regel ervaring en de weer gave TEZ View/Hive biedt een op GUI gebaseerde werk stroom.

In HDInsight 4,0 is HiveCLI vervangen door Beeline. HiveCLI is een thrift-client voor Hiveserver 1 en Beeline is een JDBC-client die toegang biedt tot Hiveserver 2. Beeline kan ook worden gebruikt om verbinding te maken met een ander data base-eind punt dat compatibel is met JDBC. Beeline is out-of-box beschikbaar op HDInsight 4,0 zonder dat hiervoor een installatie nodig is.

In HDInsight 3,6 is de GUI-client voor interactie met hive-server de Hive-weer gave Ambari. HDInsight 4,0 vervangt de Hive-weer gave met Hortonworks Data Analytics Studio (DAS). DAS wordt niet verzonden met HDInsight-clusters out-of-Box en is geen officieel ondersteund pakket. DAS kan echter als volgt worden geïnstalleerd op het cluster met behulp van een [script actie](../hdinsight-hadoop-customize-cluster-linux.md) :

|Eigenschap | Waarde |
|---|---|
|Script type|-Aangepast|
|Naam|DAS|
|Bash-script-URI|`https://hdiconfigactions.blob.core.windows.net/dasinstaller/LaunchDASInstaller.sh`|
|Knooppunt type (n)|Head|

Wacht 5 tot 10 minuten en start vervolgens Data Analytics Studio met behulp van deze URL: `https://CLUSTERNAME.azurehdinsight.net/das/`.

Als DAS is geïnstalleerd en u de query's die u hebt uitgevoerd, niet in de query viewer ziet, voert u de volgende stappen uit:

1. Stel de configuraties voor Hive, TEZ en DAS in, zoals beschreven in [deze hand leiding voor het oplossen van problemen met das-installatie](https://docs.hortonworks.com/HDPDocuments/DAS/DAS-1.2.0/troubleshooting/content/das_queries_not_appearing.html).
2. Zorg ervoor dat de volgende Azure Storage Directory-configuraties pagina-blobs zijn en dat ze worden vermeld onder `fs.azure.page.blob.dirs`:
    * `hive.hook.proto.base-directory`
    * `tez.history.logging.proto-base-dir`
3. Start HDFS, Hive, TEZ en DAS op beide hoofd knooppunten.

## <a name="next-steps"></a>Volgende stappen

* [Aankondiging van HDInsight 4,0](../hdinsight-version-release.md)
* [HDInsight 4,0 dieper](https://azure.microsoft.com/blog/deep-dive-into-azure-hdinsight-4-0/)
* [Hive 3 zuren-tabellen](https://docs.hortonworks.com/HDPDocuments/HDP3/HDP-3.1.0/using-hiveql/content/hive_3_internals.html)
