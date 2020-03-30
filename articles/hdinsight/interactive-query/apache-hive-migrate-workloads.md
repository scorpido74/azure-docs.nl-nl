---
title: Azure HDInsight 3.6 Hive-workloads migreren naar HDInsight 4.0
description: Meer informatie over het migreren van Apache Hive-workloads op HDInsight 3.6 naar HDInsight 4.0.
author: msft-tacox
ms.author: tacox
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.date: 11/13/2019
ms.openlocfilehash: ec96189185a06c1fcbd95eed6216ade47f3089c3
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79214651"
---
# <a name="migrate-azure-hdinsight-36-hive-workloads-to-hdinsight-40"></a>Azure HDInsight 3.6 Hive-workloads migreren naar HDInsight 4.0

In dit document ziet u hoe u Apache Hive- en LLAP-workloads migreert op HDInsight 3.6 naar HDInsight 4.0. HDInsight 4.0 biedt nieuwere Hive- en LLAP-functies, zoals gematerialiseerde weergaven en queryresultaten. Wanneer u uw workloads migreert naar HDInsight 4.0, u veel nieuwere functies van Hive 3 gebruiken die niet beschikbaar zijn op HDInsight 3.6.

Dit artikel behandelt de volgende onderwerpen:

* Migratie van Hive-metagegevens naar HDInsight 4.0
* Veilige migratie van ACID- en niet-ACID-tabellen
* Behoud van hive-beveiligingsbeleid in HDInsight-versies
* Query-uitvoering en debuggen van HDInsight 3.6 naar HDInsight 4.0

Een voordeel van Hive is de mogelijkheid om metadata te exporteren naar een externe database (aangeduid als de Hive Metastore). De **Hive Metastore** is verantwoordelijk voor het opslaan van tabelstatistieken, waaronder de locatie van de tabelopslag, kolomnamen en tabelindexgegevens. Het metastore-databaseschema verschilt tussen Hive-versies. De aanbevolen manier om de Hive metastore veilig te upgraden is door een kopie te maken en de kopie te upgraden in plaats van de huidige productieomgeving.

## <a name="copy-metastore"></a>Metastore kopiëren

HDInsight 3.6 en HDInsight 4.0 vereisen verschillende metastoreschema's en kunnen geen enkele metastore delen.

### <a name="external-metastore"></a>Externe metastore

Maak een nieuwe kopie van uw externe metastore. Als u een externe metastore gebruikt, is een van de veilige en eenvoudige manieren om een kopie van de metastore te maken het herstellen van [de database](../../sql-database/sql-database-recovery-using-backups.md#point-in-time-restore) met een andere naam met behulp van de sql-databaseherstelfunctie.  Zie [Externe metagegevensopslag gebruiken in Azure HDInsight](../hdinsight-use-external-metadata-stores.md) voor meer informatie over het koppelen van een externe metastore aan een HDInsight-cluster.

### <a name="internal-metastore"></a>Interne metastore

Als u de interne metastore gebruikt, u query's gebruiken om objectdefinities in de Hive-metastore te exporteren en deze in een nieuwe database te importeren.

Zodra dit script is voltooid, wordt ervan uitgegaan dat het oude cluster niet langer wordt gebruikt voor toegang tot een van de tabellen of databases waarnaar in het script wordt verwezen.

> [!NOTE]
> In het geval van ACID-tabellen wordt een nieuwe kopie van de gegevens onder de tabel gemaakt.

1. Maak verbinding met het HDInsight-cluster via een [Secure Shell -client (SSH).](../hdinsight-hadoop-linux-use-ssh-unix.md)

1. Maak verbinding met HiveServer2 met uw [Beeline-client](../hadoop/apache-hadoop-use-hive-beeline.md) vanuit uw geopende SSH-sessie door de volgende opdracht in te voeren:

    ```hiveql
    for d in `beeline -u "jdbc:hive2://localhost:10001/;transportMode=http" --showHeader=false --silent=true --outputformat=tsv2 -e "show databases;"`; 
    do
        echo "Scanning Database: $d"
        echo "create database if not exists $d; use $d;" >> alltables.hql; 
        for t in `beeline -u "jdbc:hive2://localhost:10001/$d;transportMode=http" --showHeader=false --silent=true --outputformat=tsv2 -e "show tables;"`;
        do
            echo "Copying Table: $t"
            ddl=`beeline -u "jdbc:hive2://localhost:10001/$d;transportMode=http" --showHeader=false --silent=true --outputformat=tsv2 -e "show create table $t;"`;

            echo "$ddl;" >> alltables.hql;
            lowerddl=$(echo $ddl | awk '{print tolower($0)}')
            if [[ $lowerddl == *"'transactional'='true'"* ]]; then
                if [[ $lowerddl == *"partitioned by"* ]]; then
                    # partitioned
                    raw_cols=$(beeline -u "jdbc:hive2://localhost:10001/$d;transportMode=http" --showHeader=false --silent=true --outputformat=tsv2 -e "show create table $t;" | tr '\n' ' ' | grep -io "CREATE TABLE .*" | cut -d"(" -f2- | cut -f1 -d")" | sed 's/`//g');
                    ptn_cols=$(beeline -u "jdbc:hive2://localhost:10001/$d;transportMode=http" --showHeader=false --silent=true --outputformat=tsv2 -e "show create table $t;" | tr '\n' ' ' | grep -io "PARTITIONED BY .*" | cut -f1 -d")" | cut -d"(" -f2- | sed 's/`//g');
                    final_cols=$(echo "(" $raw_cols "," $ptn_cols ")")

                    beeline -u "jdbc:hive2://localhost:10001/$d;transportMode=http" --showHeader=false --silent=true --outputformat=tsv2 -e "create external table ext_$t $final_cols TBLPROPERTIES ('transactional'='false');";
                    beeline -u "jdbc:hive2://localhost:10001/$d;transportMode=http" --showHeader=false --silent=true --outputformat=tsv2 -e "insert into ext_$t select * from $t;";
                    staging_ddl=`beeline -u "jdbc:hive2://localhost:10001/$d;transportMode=http" --showHeader=false --silent=true --outputformat=tsv2 -e "show create table ext_$t;"`;
                    dir=$(echo $staging_ddl | grep -io " LOCATION .*" | grep -m1 -o "'.*" | sed "s/'[^-]*//2g" | cut -c2-);

                    parsed_ptn_cols=$(echo $ptn_cols| sed 's/ [a-z]*,/,/g' | sed '$s/\w*$//g');
                    echo "create table flattened_$t $final_cols;" >> alltables.hql;
                    echo "load data inpath '$dir' into table flattened_$t;" >> alltables.hql;
                    echo "insert into $t partition($parsed_ptn_cols) select * from flattened_$t;" >> alltables.hql;
                    echo "drop table flattened_$t;" >> alltables.hql;
                    beeline -u "jdbc:hive2://localhost:10001/$d;transportMode=http" --showHeader=false --silent=true --outputformat=tsv2 -e "drop table ext_$t";
                else
                    # not partitioned
                    beeline -u "jdbc:hive2://localhost:10001/$d;transportMode=http" --showHeader=false --silent=true --outputformat=tsv2 -e "create external table ext_$t like $t TBLPROPERTIES ('transactional'='false');";
                    staging_ddl=`beeline -u "jdbc:hive2://localhost:10001/$d;transportMode=http" --showHeader=false --silent=true --outputformat=tsv2 -e "show create table ext_$t;"`;
                    dir=$(echo $staging_ddl | grep -io " LOCATION .*" | grep -m1 -o "'.*" | sed "s/'[^-]*//2g" | cut -c2-);

                    beeline -u "jdbc:hive2://localhost:10001/$d;transportMode=http" --showHeader=false --silent=true --outputformat=tsv2 -e "insert into ext_$t select * from $t;";
                    echo "load data inpath '$dir' into table $t;" >> alltables.hql;
                    beeline -u "jdbc:hive2://localhost:10001/$d;transportMode=http" --showHeader=false --silent=true --outputformat=tsv2 -e "drop table ext_$t";
                fi
            fi
            echo "$ddl" | grep -q "PARTITIONED\s*BY" && echo "MSCK REPAIR TABLE $t;" >> alltables.hql;
        done;
    done
    ```

    Met deze opdracht genereert u een bestand met de naam **alltables.hql.**

1. Sluit uw SSH-sessie af. Voer vervolgens een scp-opdracht in om **alltables.hql** lokaal te downloaden.

    ```bash
    scp sshuser@CLUSTERNAME-ssh.azurehdinsight.net:alltables.hql c:/hdi
    ```

1. Upload **alltables.hql** naar het *nieuwe* HDInsight-cluster.

    ```bash
    scp c:/hdi/alltables.hql sshuser@CLUSTERNAME-ssh.azurehdinsight.net:/home/sshuser/
    ```

1. Gebruik vervolgens SSH om verbinding te maken met het *nieuwe* HDInsight-cluster. Voer de volgende code uit van de SSH-sessie:

    ```bash
    beeline -u "jdbc:hive2://localhost:10001/;transportMode=http" -i alltables.hql
    ```


## <a name="upgrade-metastore"></a>Metastore upgraden

Zodra de **metastore-kopie** is voltooid, voert u een schema-upgradescript uit in [Scriptaction](../hdinsight-hadoop-customize-cluster-linux.md) op het bestaande HDInsight 3.6-cluster om het nieuwe metastore te upgraden naar Hive 3-schema. Hierdoor kan de database worden gekoppeld als HDInsight 4.0 metastore.

Gebruik de waarden in de onderstaande tabel. Vervang `SQLSERVERNAME DATABASENAME USERNAME PASSWORD` door de juiste waarden voor de **gekopieerde** Hive-metastore, gescheiden door spaties. Vermeld ".database.windows.net" niet bij het opgeven van de SQL-servernaam.

|Eigenschap | Waarde |
|---|---|
|Scripttype|- Aangepast|
|Name|Hive-upgrade|
|Bash script URI|`https://hdiconfigactions.blob.core.windows.net/hivemetastoreschemaupgrade/launch-schema-upgrade.sh`|
|Knooppunttype(s)|Head|
|Parameters|WACHTWOORD VOOR GEBRUIKERSNAAM VAN SQLSERVERNAME-DATABASENAAM|

> [!Warning]  
> De upgrade waarmee het HDInsight 3.6-metadataschema wordt omgezet naar het HDInsight 4.0-schema, kan niet worden teruggedraaid.

U de upgrade verifiëren door de volgende sql-query uit te voeren tegen de database:

```sql
select * from dbo.version
```

## <a name="migrate-hive-tables-to-hdinsight-40"></a>Hive-tabellen migreren naar HDInsight 4.0

Na het voltooien van de vorige reeks stappen om de Hive Metastore naar HDInsight 4.0 te migreren, zijn de tabellen en `show tables` `show databases` databases die in de metastore zijn opgenomen zichtbaar vanuit het HDInsight 4.0-cluster door het uitvoeren of vanuit het cluster. Zie [Query-uitvoering in HDInsight-versies](#query-execution-across-hdinsight-versions) voor informatie over query-uitvoering in HDInsight 4.0-clusters.

De werkelijke gegevens uit de tabellen zijn echter pas toegankelijk als het cluster toegang heeft tot de benodigde opslagaccounts. Voer de volgende stappen uit om ervoor te zorgen dat uw HDInsight 4.0-cluster toegang heeft tot dezelfde gegevens als uw oude HDInsight 3.6-cluster:

1. Bepaal het Azure-opslagaccount van uw tabel of database.

1. Als uw HDInsight 4.0-cluster al actief is, koppelt u het Azure-opslagaccount via Ambari aan het cluster. Als u het HDInsight 4.0-cluster nog niet hebt gemaakt, controleert u of het Azure-opslagaccount is opgegeven als het primaire of secundaire clusteropslagaccount. Zie [Extra opslagaccounts toevoegen aan HDInsight voor](../hdinsight-hadoop-add-storage.md)meer informatie over het toevoegen van opslagaccounts aan HDInsight.

## <a name="deploy-new-hdinsight-40-and-connect-to-the-new-metastore"></a>Implementeer nieuwe HDInsight 4.0 en maak verbinding met de nieuwe metastore

Nadat de schema-upgrade is voltooid, implementeert u een nieuw HDInsight 4.0-cluster en sluit u de geüpgradede metastore aan. Als u 4.0 al hebt geïmplementeerd, stelt u deze zo in dat u verbinding maken met de metastore van Ambari.

## <a name="run-schema-migration-script-from-hdinsight-40"></a>Schemamigratiescript uitvoeren vanaf HDInsight 4.0

Tabellen worden anders behandeld in HDInsight 3.6 en HDInsight 4.0. Daarom u niet dezelfde tabellen delen voor clusters van verschillende versies. Als u HDInsight 3.6 tegelijk met HDInsight 4.0 wilt gebruiken, moet u voor elke versie afzonderlijke kopieën van de gegevens hebben.

Uw Hive-werkbelasting kan een mix van ACID- en niet-ACID-tabellen bevatten. Een belangrijk verschil tussen Hive op HDInsight 3.6 (Hive 2) en Hive op HDInsight 4.0 (Hive 3) is ACID-compliance voor tabellen. In HDInsight 3.6 vereist het inschakelen van Hive ACID-compliance extra configuratie, maar in HDInsight 4.0-tabellen zijn standaard ACID-compatibel. De enige actie die nodig is voor migratie is het uitvoeren van een grote verdichting tegen de ACID-tabel op de 3.6 cluster. Voer in de hive-weergave of vanuit Beeline de volgende query uit:

```sql
alter table myacidtable compact 'major';
```

Deze verdichting is vereist omdat HDInsight 3.6- en HDInsight 4.0 ACID-tabellen ACID-delta's anders begrijpen. Verdichting dwingt tot een schone lei die consistentie garandeert. Deel 4 van de [Hive migratiedocumentatie](https://docs.hortonworks.com/HDPDocuments/Ambari-2.7.3.0/bk_ambari-upgrade-major/content/prepare_hive_for_upgrade.html) bevat richtlijnen voor bulkverdichting van HDInsight 3.6 ACID-tabellen.

Zodra u de migratie- en verdichtingsstappen van metastore hebt voltooid, u het werkelijke magazijn migreren. Nadat u de migratie van het Hive-magazijn hebt voltooid, heeft het magazijn HDInsight 4.0 de volgende eigenschappen:

|3.6 |4,0 |
|---|---|
|Externe tabellen|Externe tabellen|
|Niet-transactionele beheerde tabellen|Externe tabellen|
|Door transacties beheerde tabellen|Beheerde tabellen|

Mogelijk moet u de eigenschappen van uw magazijn aanpassen voordat u de migratie uitvoert. Als u bijvoorbeeld verwacht dat een bepaalde tabel wordt geopend door een derde partij (zoals een HDInsight 3.6-cluster), moet die tabel extern zijn zodra de migratie is voltooid. In HDInsight 4.0 zijn alle beheerde tabellen transactioneel. Daarom mogen beheerde tabellen in HDInsight 4.0 alleen toegankelijk zijn voor HDInsight 4.0-clusters.

Voer het hulpprogramma voor de migratie van het hive-magazijn uit vanaf een van de clusterhoofdnoden met de ssh-shell:

1. Maak verbinding met uw clusterheadnode met SSH. Zie Verbinding [maken met HDInsight met SSH voor](../hdinsight-hadoop-linux-use-ssh-unix.md) instructies
1. Een aanmeldingsshell openen als de Hive-gebruiker door`sudo su - hive`
1. Bepaal de versie van de `ls /usr/hdp`gegevensplatformstack door het uitvoeren van . Hiermee wordt een versietekenreeks weergegeven die u in de volgende opdracht moet gebruiken.
1. Voer de volgende opdracht uit vanuit de shell. Vervang `STACK_VERSION` de versietekenreeks uit de vorige stap:

```bash
/usr/hdp/STACK_VERSION/hive/bin/hive --config /etc/hive/conf --service  strictmanagedmigration --hiveconf hive.strict.managed.tables=true -m automatic --modifyManagedTables
```

Nadat de migratietool is voltooid, is uw Hive-magazijn klaar voor HDInsight 4.0.

> [!Important]  
> Beheerde tabellen in HDInsight 4.0 (inclusief tabellen gemigreerd vanaf 3.6) mogen niet toegankelijk zijn voor andere services of toepassingen, waaronder HDInsight 3.6-clusters.

## <a name="secure-hive-across-hdinsight-versions"></a>Secure Hive in HDInsight-versies

Sinds HDInsight 3.6 integreert HDInsight met Azure Active Directory met HDInsight Enterprise Security Package (ESP). ESP gebruikt Kerberos en Apache Ranger om de machtigingen van specifieke bronnen binnen het cluster te beheren. Ranger-beleid dat is geïmplementeerd tegen Hive in HDInsight 3.6 kan worden gemigreerd naar HDInsight 4.0 met de volgende stappen:

1. Navigeer naar het deelvenster Ranger Service Manager in uw HDInsight 3.6-cluster.
2. Navigeer naar het beleid met de naam **HIVE** en exporteer het beleid naar een json-bestand.
3. Zorg ervoor dat alle gebruikers waarnaar in het geëxporteerde beleid wordt verwezen, in het nieuwe cluster bestaan. Als een gebruiker wordt genoemd in het beleidsjson, maar niet bestaat in het nieuwe cluster, voegt u de gebruiker toe aan het nieuwe cluster of verwijdert u de verwijzing uit het beleid.
4. Navigeer naar het deelvenster **Ranger Service Manager** in uw HDInsight 4.0-cluster.
5. Navigeer naar het beleid met de naam **HIVE** en importeer het ranger-beleid vanaf stap 2.

## <a name="check-compatibility-and-modify-codes-as-needed-in-test-app"></a>Compatibiliteit controleren en codes wijzigen als dat nodig is in de test-app

Controleer bij het migreren van workloads, zoals bestaande programma's en query's, de releasenotes en documentatie op wijzigingen en pas wijzigingen zo nodig toe. Als uw HDInsight 3.6-cluster een gedeelde Spark- en Hive-metastore gebruikt, is [extra configuratie met Hive Warehouse Connector](./apache-hive-warehouse-connector.md) vereist.

## <a name="deploy-new-app-for-production"></a>Nieuwe app implementeren voor productie

Als u wilt overschakelen naar het nieuwe cluster, u bijvoorbeeld een nieuwe clienttoepassing installeren en gebruiken als een nieuwe productieomgeving, of u uw bestaande clienttoepassing upgraden en overschakelen naar HDInsight 4.0.

## <a name="switch-hdinsight-40-to-the-production"></a>Schakel HDInsight 4.0 over op de productie

Als er tijdens het testen verschillen zijn gemaakt in de metastore, moet u de wijzigingen bijwerken voordat u overstapt. In dit geval u de metastore exporteren & importeren en vervolgens opnieuw upgraden.

## <a name="remove-the-old-production"></a>De oude productie verwijderen

Zodra u hebt bevestigd dat de release volledig en volledig operationeel is, u versie 3.6 en de vorige metastore verwijderen. Zorg ervoor dat alles wordt gemigreerd voordat u de omgeving verwijderd.

## <a name="query-execution-across-hdinsight-versions"></a>Query-uitvoering in hdinsight-versies

Er zijn twee manieren om Hive/LLAP-query's uit te voeren en te debuggen binnen een HDInsight 3.6-cluster. HiveCLI biedt een command-line-ervaring en de Tez-weergave/Hive-weergave biedt een op GUI gebaseerde workflow.

In HDInsight 4.0 is HiveCLI vervangen door Beeline. HiveCLI is een spaarzame client voor Hiveserver 1 en Beeline is een JDBC-client die toegang biedt tot Hiveserver 2. Beeline kan ook worden gebruikt om verbinding te maken met een ander JDBC-compatibel databaseeindpunt. Beeline is kant-en-klare verkrijgbaar op HDInsight 4.0 zonder enige installatie nodig.

In HDInsight 3.6 is de GUI-client voor interactie met hive-server de Ambari Hive-weergave. HDInsight 4.0 wordt niet verzonden met Ambari View. We hebben onze klanten een manier geboden om Data Analytics Studio (DAS) te gebruiken, wat geen kernservice is voor HDInsight. DAS wordt niet out-of-the-box geleverd met HDInsight-clusters en is geen officieel ondersteund pakket. DAS kan echter als volgt op het cluster worden geïnstalleerd met behulp van een [scriptactie:](../hdinsight-hadoop-customize-cluster-linux.md)

|Eigenschap | Waarde |
|---|---|
|Scripttype|- Aangepast|
|Name|Das|
|Bash script URI|`https://hdiconfigactions.blob.core.windows.net/dasinstaller/LaunchDASInstaller.sh`|
|Knooppunttype(s)|Head|

Wacht 10 tot 15 minuten en start Data `https://CLUSTERNAME.azurehdinsight.net/das/`Analytics Studio met deze URL: .

Een vernieuwing van de Ambari-gebruikersinterface en/of een herstart van alle Ambari-componenten kan nodig zijn voordat u toegang krijgt tot DAS.

Voer de volgende stappen uit als U de query's die u hebt uitgevoerd in de viewer query's niet ziet:

1. Stel de configuraties voor Hive, Tez en DAS in zoals beschreven in [deze handleiding voor het oplossen van problemen met DAS-installatie.](https://docs.hortonworks.com/HDPDocuments/DAS/DAS-1.2.0/troubleshooting/content/das_queries_not_appearing.html)
2. Controleer of de volgende Azure-opslagmapconfigs paginablobs zijn en `fs.azure.page.blob.dirs`dat ze worden weergegeven onder:
    * `hive.hook.proto.base-directory`
    * `tez.history.logging.proto-base-dir`
3. Start HDFS, Hive, Tez en DAS opnieuw op beide headnodes.

## <a name="next-steps"></a>Volgende stappen

* [HDInsight 4.0-aankondiging](../hdinsight-version-release.md)
* [HDInsight 4.0 diepe duik](https://azure.microsoft.com/blog/deep-dive-into-azure-hdinsight-4-0/)
* [Hive 3 ACID Tafels](https://docs.hortonworks.com/HDPDocuments/HDP3/HDP-3.1.0/using-hiveql/content/hive_3_internals.html)
