---
title: Azure HDInsight 3,6 Hive-workloads migreren naar HDInsight 4,0
description: Meer informatie over het migreren van Apache Hive-workloads op HDInsight 3,6 naar HDInsight 4,0.
ms.service: hdinsight
author: msft-tacox
ms.author: tacox
ms.reviewer: jasonh
ms.topic: conceptual
ms.date: 04/24/2019
ms.openlocfilehash: 1b270663a83461ecd777599fead9d717e93482c0
ms.sourcegitcommit: 4c3d6c2657ae714f4a042f2c078cf1b0ad20b3a4
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 10/25/2019
ms.locfileid: "72930891"
---
# <a name="migrate-azure-hdinsight-36-hive-workloads-to-hdinsight-40"></a>Azure HDInsight 3,6 Hive-workloads migreren naar HDInsight 4,0

Dit document bevat informatie over het migreren van Apache Hive-en LLAP-workloads op HDInsight 3,6 naar HDInsight 4,0. HDInsight 4,0 biedt nieuwere Hive-en LLAP-functies, zoals gerealiseerde weer gaven en caches voor query resultaten. Wanneer u uw workloads migreert naar HDInsight 4,0, kunt u veel nieuwere functies van Hive 3 gebruiken die niet beschikbaar zijn op HDInsight 3,6.

In dit artikel komen de volgende onderwerpen aan bod:

* Migratie van Hive-meta gegevens naar HDInsight 4,0
* Veilige migratie van zuren en niet-zure tabellen
* Behoud van Hive-beveiligings beleid in HDInsight-versies
* Uitvoering van query's en fout opsporing van HDInsight 3,6 naar HDInsight 4,0

## <a name="migrate-apache-hive-metadata-to-hdinsight-40"></a>Apache Hive meta gegevens migreren naar HDInsight 4,0

Een voor deel van het onderdeel is de mogelijkheid om meta gegevens te exporteren naar een externe data base (waarnaar wordt verwezen als het Hive-archief). De **Hive-meta Store** is verantwoordelijk voor het opslaan van tabel statistieken, met inbegrip van de tabel opslag locatie, kolom namen en tabel index informatie. Het schema van de meta store-data base wijkt af van de Hive-versies. Voer de volgende stappen uit om een HDInsight 3,6-Hive-meta Store bij te werken, zodat deze compatibel is met HDInsight 4,0.

1. Maak een nieuwe kopie van uw externe meta Store. Voor HDInsight 3,6 en HDInsight 4,0 zijn verschillende meta Store-schema's vereist en er kan geen enkele meta Store worden gedeeld. Zie [externe meta gegevensopslag plaatsen in azure HDInsight gebruiken](../hdinsight-use-external-metadata-stores.md) voor meer informatie over het koppelen van een externe metadata voor een HDInsight-cluster. 
2. Start een script actie op basis van uw HDI 3,6-cluster, met ' hoofd knooppunten ' als knooppunt type voor uitvoering. Plak de volgende URI in het tekstvak ' bash-script-URI ': https://hdiconfigactions.blob.core.windows.net/hivemetastoreschemaupgrade/launch-schema-upgrade.sh. Voer in het tekstvak ' argumenten ' de servername, de data base, de gebruikers naam en het wacht woord in voor de **gekopieerde** Hive-metastore, gescheiden door spaties. Neem '. database.windows.net ' niet op wanneer u servername opgeeft.

> [!Warning]
> De upgrade waarmee het meta gegevens schema van HDInsight 3,6 wordt geconverteerd naar het HDInsight 4,0-schema, kan niet worden omgekeerd.

## <a name="migrate-hive-tables-to-hdinsight-40"></a>Hive-tabellen migreren naar HDInsight 4,0

Na het volt ooien van de vorige set stappen voor het migreren van de Hive-meta Store naar HDInsight 4,0, zijn de tabellen en data bases die zijn vastgelegd in de meta Store, zichtbaar vanuit het HDInsight 4,0-cluster door `show tables` of `show databases` vanuit het cluster uit te voeren. Zie [query's](#query-execution-across-hdinsight-versions) uitvoeren in hdinsight-versies voor meer informatie over het uitvoeren van Query's in hdinsight 4,0-clusters.

De werkelijke gegevens uit de tabellen zijn echter niet toegankelijk totdat het cluster toegang heeft tot de benodigde opslag accounts. Voer de volgende stappen uit om ervoor te zorgen dat uw HDInsight 4,0-cluster toegang heeft tot dezelfde gegevens als uw oude HDInsight 3,6-cluster:

1. Bepaal het Azure-opslag account van de tabel of Data Base met behulp van de indeling voor opmaak.
2. Als uw HDInsight 4,0-cluster al wordt uitgevoerd, koppelt u het Azure Storage-account aan het cluster via Ambari. Als u het HDInsight 4,0-cluster nog niet hebt gemaakt, moet u ervoor zorgen dat het Azure Storage-account is opgegeven als het primaire of secundaire cluster opslag account. Zie [extra opslag accounts toevoegen aan hdinsight](../hdinsight-hadoop-add-storage.md)voor meer informatie over het toevoegen van opslag accounts aan hdinsight-clusters.

> [!Note]
> Tabellen worden anders behandeld in HDInsight 3,6 en HDInsight 4,0. Daarom kunt u niet dezelfde tabellen delen voor clusters van verschillende versies. Als u HDInsight 3,6 op hetzelfde moment als HDInsight 4,0 wilt gebruiken, moet u afzonderlijke exemplaren van de gegevens voor elke versie hebben.

Uw Hive-workload kan bestaan uit een combi natie van zuren en niet-zure tabellen. Een belang rijk verschil tussen Hive op HDInsight 3,6 (hive 2) en Hive op HDInsight 4,0 (Hive 3) is zuur bestendig voor tabellen. In HDInsight 3,6 is aanvullende configuratie vereist, maar in HDInsight 4,0-tabellen zijn standaard ACID-compatibel. De enige vereiste actie voor de migratie is het uitvoeren van een grote compressie op basis van de tabel zuur op het 3,6-cluster. Voer de volgende query uit vanuit de Hive-weer gave of vanuit beeline:

```bash
alter table myacidtable compact 'major';
```

Deze compressie is vereist omdat de tabellen HDInsight 3,6 en HDInsight 4,0 ACID een andere betekenis hebben. Door compressie wordt een schone pastel afgedwongen die consistentie waarborgt. Sectie 4 van de [documentatie voor Hive-migratie](https://docs.hortonworks.com/HDPDocuments/Ambari-2.7.3.0/bk_ambari-upgrade-major/content/prepare_hive_for_upgrade.html) bevat richt lijnen voor bulk compressie van HDINSIGHT 3,6 zuren-tabellen.

Wanneer u de migratie en de compressie stappen van de meta Store hebt voltooid, kunt u het werkelijke magazijn migreren. Nadat u de Hive-Warehouse migratie hebt voltooid, heeft het HDInsight 4,0-magazijn de volgende eigenschappen:

* Externe tabellen in HDInsight 3,6 worden externe tabellen in HDInsight 4,0
* Niet-transactionele beheerde tabellen in HDInsight 3,6 zijn externe tabellen in HDInsight 4,0
* Transactionele beheerde tabellen in HDInsight 3,6 worden beheerde tabellen in HDInsight 4,0

Mogelijk moet u de eigenschappen van uw magazijn aanpassen voordat u de migratie uitvoert. Als u bijvoorbeeld verwacht dat een bepaalde tabel wordt gebruikt door een derde partij (zoals een HDInsight 3,6-cluster), moet deze tabel extern zijn zodra de migratie is voltooid. In HDInsight 4,0 zijn alle beheerde tabellen transactioneel. Beheerde tabellen in HDInsight 4,0 mogen daarom alleen worden gebruikt door HDInsight 4,0-clusters.

Zodra de tabel eigenschappen correct zijn ingesteld, voert u het hulp programma voor het migratie hulpprogramma voor het Hive-magazijn uit vanuit een van de cluster-hoofd knooppunten met behulp van de SSH-Shell:

1. Maak via SSH verbinding met uw cluster-hoofd knooppunt. Zie [verbinding maken met HDInsight via SSH](../hdinsight-hadoop-linux-use-ssh-unix.md) voor instructies.
1. Open een aanmeldings shell als de Hive-gebruiker door uit te voeren `sudo su - hive`
1. Bepaal de versie van de gegevens platform stack door `ls /usr/hdp`uit te voeren. Hiermee wordt een versie teken reeks weer gegeven die u moet gebruiken in de volgende opdracht.
1. Voer de volgende opdracht uit vanuit de shell. Vervang `${{STACK_VERSION}}` door de versie teken reeks uit de vorige stap:

```bash
/usr/hdp/${{STACK_VERSION}}/hive/bin/hive --config /etc/hive/conf --service  strictmanagedmigration --hiveconf hive.strict.managed.tables=true -m automatic --modifyManagedTables
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

## <a name="query-execution-across-hdinsight-versions"></a>Uitvoering van query's in HDInsight-versies

Er zijn twee manieren voor het uitvoeren en opsporen van fouten in Hive/LLAP-query's binnen een HDInsight 3,6-cluster. HiveCLI biedt een opdracht regel ervaring en de weer gave TEZ View/Hive biedt een op GUI gebaseerde werk stroom.

In HDInsight 4,0 is HiveCLI vervangen door Beeline. HiveCLI is een thrift-client voor Hiveserver 1 en Beeline is een JDBC-client die toegang biedt tot Hiveserver 2. Beeline kan ook worden gebruikt om verbinding te maken met een ander data base-eind punt dat compatibel is met JDBC. Beeline is out-of-box beschikbaar op HDInsight 4,0 zonder dat hiervoor een installatie nodig is.

In HDInsight 3,6 is de GUI-client voor interactie met hive-server de Hive-weer gave Ambari. HDInsight 4,0 vervangt de Hive-weer gave met Hortonworks Data Analytics Studio (DAS). DAS wordt niet meegeleverd met HDInsight-clusters en is geen officieel ondersteund pakket. DAS kan echter als volgt op het cluster worden geïnstalleerd:

Start een script actie op basis van uw cluster, met ' hoofd knooppunten ' als knooppunt type voor uitvoering. Plak de volgende URI in het tekstvak ' bash-script-URI ': https://hdiconfigactions.blob.core.windows.net/dasinstaller/LaunchDASInstaller.sh

Wacht 5 tot 10 minuten en start vervolgens Data Analytics Studio met behulp van deze URL: https://\<clustername >. azurehdinsight. net/das/

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
