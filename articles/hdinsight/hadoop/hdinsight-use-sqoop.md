---
title: Apache Sqoop-taken uitvoeren met Azure HDInsight (Apache Hadoop)
description: Meer informatie over het gebruik van Azure PowerShell vanuit een werkstation om Sqoop-import en export uit te voeren tussen een Hadoop-cluster en een Azure SQL-database.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.date: 12/06/2019
ms.openlocfilehash: 8353c0fba034022a79570d09b320b7b5c4c3e60a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "74951850"
---
# <a name="use-apache-sqoop-with-hadoop-in-hdinsight"></a>Apache Sqoop gebruiken met Hadoop in HDInsight

[!INCLUDE [sqoop-selector](../../../includes/hdinsight-selector-use-sqoop.md)]

Meer informatie over het gebruik van Apache Sqoop in HDInsight voor het importeren en exporteren van gegevens tussen een HDInsight-cluster en een Azure SQL-database.

Hoewel Apache Hadoop een natuurlijke keuze is voor het verwerken van ongestructureerde en semi-gestructureerde gegevens, zoals logboeken en bestanden, kan het ook nodig zijn om gestructureerde gegevens te verwerken die zijn opgeslagen in relationele databases.

[Apache Sqoop](https://sqoop.apache.org/docs/1.99.7/user.html) is een tool ontworpen om gegevens over te dragen tussen Hadoop clusters en relationele databases. U het gebruiken om gegevens uit een relationeel databasebeheersysteem (RDBMS) zoals SQL Server, MySQL of Oracle te importeren in het Hadoop distributed file system (HDFS), de gegevens in Hadoop om te zetten met MapReduce of Apache Hive en de gegevens vervolgens terug te exporteren naar een RDBMS . In dit artikel gebruikt u een SQL Server-database voor uw relationele database.

> [!IMPORTANT]  
> In dit artikel wordt een testomgeving ingesteld om de gegevensoverdracht uit te voeren. Vervolgens kiest u een methode voor gegevensoverdracht voor deze omgeving uit een van de methoden in sectie [Sqoop-taken uitvoeren,](#run-sqoop-jobs)verder hieronder.

Zie Nieuwe versies van de cluster die door HDInsight worden geleverd voor Sqoop-versies die worden ondersteund op [HDInsight-clusters?](../hdinsight-component-versioning.md)

## <a name="understand-the-scenario"></a>Inzicht in het scenario

HDInsight cluster wordt geleverd met een aantal voorbeeldgegevens. U gebruikt de volgende twee voorbeelden:

* Een Apache Log4j log bestand, `/example/data/sample.log`dat zich bevindt op . De volgende logboeken worden uit het bestand gehaald:

```text
2012-02-03 18:35:34 SampleClass6 [INFO] everything normal for id 577725851
2012-02-03 18:35:34 SampleClass4 [FATAL] system problem at id 1991281254
2012-02-03 18:35:34 SampleClass3 [DEBUG] detail for id 1304807656
...
```

* Een Hive-tabel met de naam `hivesampletable`, `/hive/warehouse/hivesampletable`die verwijst naar het gegevensbestand op . De tabel bevat enkele gegevens van mobiele apparaten.
  
  | Veld | Gegevenstype |
  | --- | --- |
  | clientid |tekenreeks |
  | querytijd |tekenreeks |
  | markt |tekenreeks |
  | deviceplatform |tekenreeks |
  | devicemake |tekenreeks |
  | apparaatmodel |tekenreeks |
  | state |tekenreeks |
  | land |tekenreeks |
  | querydwelltime |double |
  | Sessionid |bigint |
  | sessiepaginaweergavevolgorde |bigint |

In dit artikel gebruikt u deze twee gegevenssets om sqoop-import en export te testen.

## <a name="set-up-test-environment"></a><a name="create-cluster-and-sql-database"></a>Testomgeving instellen

Het cluster, de SQL-database en andere objecten worden gemaakt via de Azure-portal met behulp van een Azure Resource Manager-sjabloon. De sjabloon is te vinden in [Azure quickstart-sjablonen.](https://azure.microsoft.com/resources/templates/101-hdinsight-linux-with-sql-database/) De resourcemanagersjabloon roept een bacpac-pakket aan om de tabelschema's te implementeren in een SQL-database.  Het bacpac-pakket bevindt zich https://hditutorialdata.blob.core.windows.net/usesqoop/SqoopTutorial-2016-2-23-11-2.bacpacin een openbare blobcontainer. Als u een privécontainer voor de bacpac-bestanden wilt gebruiken, gebruikt u de volgende waarden in de sjabloon:

```json
"storageKeyType": "Primary",
"storageKey": "<TheAzureStorageAccountKey>",
```

> [!NOTE]  
> Importeren met behulp van een sjabloon of de Azure-portal ondersteunt alleen het importeren van een BACPAC-bestand uit Azure blob-opslag.

1. Selecteer de volgende afbeelding om de sjabloon Resourcebeheer in de Azure-portal te openen.

    <a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-hdinsight-linux-with-sql-database%2Fazuredeploy.json" target="_blank"><img src="./media/hdinsight-use-sqoop/hdi-deploy-to-azure1.png" alt="Deploy to Azure button for new cluster"></a>

2. Voer de volgende eigenschappen in:

    |Veld |Waarde |
    |---|---|
    |Abonnement |Selecteer uw Azure-abonnement in de vervolgkeuzelijst.|
    |Resourcegroep |Selecteer uw resourcegroep in de vervolgkeuzelijst of maak een nieuwe groep|
    |Locatie |Selecteer een gebied in de vervolgkeuzelijst.|
    |Clusternaam |Voer een naam in voor het Hadoop-cluster. Gebruik alleen kleine letters.|
    |Gebruikersnaam voor clusteraanmelding |Houd de vooraf ingevulde waarde `admin`.|
    |Wachtwoord voor clusteraanmelding |Voer een wachtwoord in.|
    |Ssh-gebruikersnaam |Houd de vooraf ingevulde waarde `sshuser`.|
    |Ssh-wachtwoord |Voer een wachtwoord in.|
    |Sql-beheer aanmelding |Houd de vooraf ingevulde waarde `sqluser`.|
    |Sql-beheerwachtwoord |Voer een wachtwoord in.|
    |_artifacts locatie | Gebruik de standaardwaarde, tenzij u uw eigen bacpac-bestand op een andere locatie wilt gebruiken.|
    |_artifacts Locatie Sas-token |Leeg laten.|
    |Bacpac-bestandsnaam |Gebruik de standaardwaarde, tenzij u uw eigen bacpac-bestand wilt gebruiken.|
    |Locatie |Gebruik de standaardwaarde.|

    De naam Azure SQL `<ClusterName>dbserver`Server is . De naam van `<ClusterName>db`de database is . De standaardnaam van `e6qhezrh2pdqu`het opslagaccount is .

3. Selecteer **Ik ga akkoord met de hierboven vermelde algemene voorwaarden.**

4. Selecteer **Aankoop**. U ziet een nieuwe tegel met de titel Implementatie indienen voor sjabloonimplementatie. Het duurt ongeveer 20 minuten om het cluster en de SQL-database te maken.

## <a name="run-sqoop-jobs"></a>Sqoop-taken uitvoeren

HDInsight kan Sqoop-taken uitvoeren met behulp van verschillende methoden. Gebruik de volgende tabel om te beslissen welke methode geschikt is voor u en volg vervolgens de koppeling voor een walkthrough.

| **Gebruik dit** als je wilt ... | ... een **interactieve** shell | ... **verwerking van partij** | ... van dit **besturingssysteem** van de client |
|:--- |:---:|:---:|:--- |:--- |
| [Ssh](apache-hadoop-use-sqoop-mac-linux.md) |? |? |Linux, Unix, Mac OS X of Windows |
| [.NET-SDK voor Hadoop](apache-hadoop-use-sqoop-dotnet-sdk.md) |&nbsp; |?  |Windows (voor nu) |
| [Azure PowerShell](apache-hadoop-use-sqoop-powershell.md) |&nbsp; |? |Windows |

## <a name="limitations"></a>Beperkingen

* Bulkexport - Met HDInsight op Basis van Linux biedt de Sqoop-connector die wordt gebruikt om gegevens te exporteren naar Microsoft SQL Server of Azure SQL Database momenteel geen bulkinserts.
* Batching - Met HdInsight op Basis `-batch` van Linux voert Sqoop bij het gebruik van de switch bij het uitvoeren van wisselplaten meerdere wisselplaten uit in plaats van de invoegbewerkingen te batcheren.

## <a name="next-steps"></a>Volgende stappen

Nu heb je geleerd hoe je Sqoop moet gebruiken. Voor meer informatie zie:

* [Apache Hive gebruiken met HDInsight](../hdinsight-use-hive.md)
* [Gegevens uploaden naar HDInsight:](../hdinsight-upload-data.md)zoek andere methoden voor het uploaden van gegevens naar HDInsight/Azure Blob-opslag.
* [Apache Sqoop gebruiken voor het importeren en exporteren van gegevens tussen Apache Hadoop in HDInsight en SQL Database](./apache-hadoop-use-sqoop-mac-linux.md)