---
title: Een HBase-cluster migreren naar een nieuwe versie - Azure HDInsight
description: Apache HBase-clusters migreren naar een nieuwere versie in Azure HDInsight.
author: ashishthaps
ms.author: ashishth
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.custom: hdinsightactive
ms.date: 01/02/2020
ms.openlocfilehash: 30cda7a83feddaeb41385252a61d1dc68a881a47
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75646503"
---
# <a name="migrate-an-apache-hbase-cluster-to-a-new-version"></a>Een Apache HBase-cluster migreren naar een nieuwe versie

In dit artikel worden de stappen besproken die nodig zijn om uw Apache HBase-cluster op Azure HDInsight bij te werken naar een nieuwere versie.

De downtime tijdens het upgraden moet minimaal zijn, in de orde van minuten. Deze downtime wordt veroorzaakt door de stappen om alle in-memory gegevens door te spoelen, vervolgens de tijd om de services op het nieuwe cluster te configureren en opnieuw te starten. Uw resultaten variëren, afhankelijk van het aantal knooppunten, de hoeveelheid gegevens en andere variabelen.

## <a name="review-apache-hbase-compatibility"></a>Bekijk apache HBase-compatibiliteit

Voordat apache HBase wordt geüpgraded, moet u ervoor zorgen dat de HBase-versies op de bron- en doelclusters compatibel zijn. Zie voor meer informatie [Apache Hadoop-componenten en -versies die beschikbaar zijn met HDInsight](../hdinsight-component-versioning.md).

> [!NOTE]  
> We raden u ten zeerste aan de compatibiliteitsmatrix van de versie in het [HBase-boek te](https://hbase.apache.org/book.html#upgrading)bekijken. Eventuele brekende onverenigbaarheden moeten worden beschreven in de releasenotes van de HBase-versie.

Hier is een voorbeeld versie compatibiliteit matrix. Y geeft compatibiliteit aan en N geeft een mogelijke onverenigbaarheid aan:

| Compatibiliteitstype | Belangrijke versie| Secundaire versie | Patch |
| --- | --- | --- | --- |
| Compatibiliteit met clientserverdraad | N | J | J |
| Serverservercompatibiliteit | N | J | J |
| Compatibiliteit met bestandsindeling | N | J | J |
| Client-API-compatibiliteit | N | J | J |
| Binaire compatibiliteit van client | N | N | J |
| **Beperkte API-compatibiliteit aan serverzijde** |  |  |  |
| Stabiel | N | J | J |
| Evoluerende | N | N | J |
| Unstable | N | N | N |
| Afhankelijkheidscompatibiliteit | N | J | J |
| Operationele compatibiliteit | N | N | J |

## <a name="upgrade-with-same-apache-hbase-major-version"></a>Upgraden met dezelfde Apache HBase-hoofdversie

Voer de volgende stappen uit om uw Apache HBase-cluster op Azure HDInsight te upgraden:

1. Zorg ervoor dat uw toepassing compatibel is met de nieuwe versie, zoals weergegeven in de Compatibiliteitsmatrix en releasenotes van HBase. Test uw toepassing in een cluster met de doelversie van HDInsight en HBase.

1. [Stel een nieuw HDInsight-cluster voor bestemming in](../hdinsight-hadoop-provision-linux-clusters.md) met hetzelfde opslagaccount, maar met een andere containernaam:

    ![Gebruik hetzelfde opslagaccount, maar maak een andere container](./media/apache-hbase-migrate-new-version/same-storage-different-container.png)

1. Spoel uw bron-HBase-cluster door, het cluster dat u aan het upgraden bent. HBase schrijft binnenkomende gegevens naar een in-memory store, genaamd een _memstore._ Nadat de memstore een bepaalde grootte heeft bereikt, spoelt HBase deze door naar de schijf voor langdurige opslag in het opslagaccount van het cluster. Bij het verwijderen van het oude cluster worden de memstores gerecycled, waardoor mogelijk gegevens verloren gaan. Als u de memstore voor elke tabel handmatig wilt doorspoelen naar de schijf, voert u het volgende script uit. De nieuwste versie van dit script staat op Azure's [GitHub.](https://raw.githubusercontent.com/Azure/hbase-utils/master/scripts/flush_all_tables.sh)

    ```bash
    #!/bin/bash
    
    #-------------------------------------------------------------------------------#
    # SCRIPT TO FLUSH ALL HBASE TABLES.
    #-------------------------------------------------------------------------------#
    
    LIST_OF_TABLES=/tmp/tables.txt
    HBASE_SCRIPT=/tmp/hbase_script.txt
    TARGET_HOST=$1
    
    usage ()
    {
        if [[ "$1" == "-h" ]] || [[ "$1" == "--help" ]]
        then
            cat << ...
    
    Usage: 
    
    $0 [hostname]
    
    Providing hostname is optional and not required when the script is executed within HDInsight cluster with access to 'hbase shell'.
    
    However hostname should be provided when executing the script as a script-action from HDInsight portal.
    
    For Example:
    
        1.  Executing script inside HDInsight cluster (where 'hbase shell' is 
            accessible):
    
            $0 
    
            [No need to provide hostname]
    
        2.  Executing script from HDinsight Azure portal:
    
            Provide Script URL.
    
            Provide hostname as a parameter (i.e. hn0, hn1, hn2.. or wn2 etc.).
    ...
            exit
        fi
    }
    
    validate_machine ()
    {
        THIS_HOST=`hostname`
    
        if [[ ! -z "$TARGET_HOST" ]] && [[ $THIS_HOST  != $TARGET_HOST* ]]
        then
            echo "[INFO] This machine '$THIS_HOST' is not the right machine ($TARGET_HOST) to execute the script."
            exit 0
        fi
    }
    
    get_tables_list ()
    {
    hbase shell << ... > $LIST_OF_TABLES 2> /dev/null
        list
        exit
    ...
    }
    
    add_table_for_flush ()
    {
        TABLE_NAME=$1
        echo "[INFO] Adding table '$TABLE_NAME' to flush list..."
        cat << ... >> $HBASE_SCRIPT
            flush '$TABLE_NAME'
    ...
    }
    
    clean_up ()
    {
        rm -f $LIST_OF_TABLES
        rm -f $HBASE_SCRIPT
    }
    
    ########
    # MAIN #
    ########
    
    usage $1
    
    validate_machine
    
    clean_up
    
    get_tables_list
    
    START=false
    
    while read LINE 
    do 
        if [[ $LINE == TABLE ]] 
        then
            START=true
            continue
        elif [[ $LINE == *row*in*seconds ]]
        then
            break
        elif [[ $START == true ]]
        then
            add_table_for_flush $LINE
        fi
    
    done < $LIST_OF_TABLES
    
    cat $HBASE_SCRIPT
    
    hbase shell $HBASE_SCRIPT << ... 2> /dev/null
    exit
    ...
    
    ```

1. Stop de inname van het oude HBase-cluster.

1. Voer het vorige script opnieuw uit om ervoor te zorgen dat recente gegevens in de memstore worden doorgespoeld.

1. Meld u aan bij [Apache Ambari](https://ambari.apache.org/) op het oude cluster ( )`https://OLDCLUSTERNAME.azurehdidnsight.net`en stop de HBase-services. Wanneer u hebt gevraagd om te bevestigen dat u de services wilt stoppen, schakelt u het selectievakje in om de onderhoudsmodus voor HBase in te schakelen. Zie [CLUSTERS voor HDInsight beheren met behulp van de Ambari Web UI](../hdinsight-hadoop-manage-ambari.md)voor meer informatie over het verbinden met en het gebruik van Ambari.

    ![Klik in Ambari op Services > HBase > Stop onder Serviceacties](./media/apache-hbase-migrate-new-version/stop-hbase-services1.png)

    ![Schakel de onderhoudsmodus inschakelen voor HBase en bevestig vervolgens](./media/apache-hbase-migrate-new-version/turn-on-maintenance-mode.png)

1. Meld u aan bij Ambari op het nieuwe HDInsight-cluster. Wijzig `fs.defaultFS` de HDFS-instelling om de containernaam aan te wijzen die door het oorspronkelijke cluster wordt gebruikt. Deze instelling staat onder **HDFS > Configs > Advanced > Advanced core-site.**

    ![Klik in Ambari op Services > HDFS > Configs > Advanced](./media/apache-hbase-migrate-new-version/hdfs-advanced-settings.png)

    ![Wijzig in Ambari de containernaam](./media/apache-hbase-migrate-new-version/change-container-name.png)

1. Als u geen HBase-clusters gebruikt met de functie Verbeterde schrijfbewerkingen, slaat u deze stap over. Het is alleen nodig voor HBase-clusters met de functie Verbeterde schrijfbewerkingen.

   Wijzig `hbase.rootdir` het pad dat naar de container van het oorspronkelijke cluster moet wijzen.

    ![Wijzig in Ambari de containernaam voor HBase-rootdir](./media/apache-hbase-migrate-new-version/change-container-name-for-hbase-rootdir.png)

1. Als u HDInsight 3.6 naar 4.0 upgradet, volgt u de onderstaande stappen, ga anders naar stap 10:
    1. Start alle vereiste services opnieuw in Ambari door **Services** > **Opnieuw opstarten Te selecteren.**
    1. Stop de HBase-service.
    1. SSH naar het Zookeeper-knooppunt en voer `rmr /hbase-unsecure` het [zkCli-commando](https://github.com/go-zkcli/zkcli) uit om de HBase-wortelznode van Zookeeper te verwijderen.
    1. Start HBase opnieuw op.

1. Als u naast 4.0 een upgrade uitvoert naar een andere HDInsight-versie, voert u de volgende stappen uit:
    1. Sla uw wijzigingen op.
    1. Start alle vereiste services opnieuw, zoals ambari aangeeft.

1. Wijs uw toepassing naar het nieuwe cluster.

    > [!NOTE]  
    > De statische DNS voor uw toepassing verandert bij het upgraden. In plaats van deze DNS hard te coderen, u een CNAME configureren in de DNS-instellingen van uw domeinnaam die naar de naam van het cluster verwijst. Een andere optie is om een configuratiebestand voor uw toepassing te gebruiken dat u bijwerken zonder opnieuw te implementeren.

1. Start de opname om te zien of alles werkt zoals verwacht.

1. Als het nieuwe cluster bevredigend is, verwijdert u het oorspronkelijke cluster.

## <a name="next-steps"></a>Volgende stappen

Zie de volgende artikelen voor meer informatie over [Apache HBase](https://hbase.apache.org/) en het upgraden van HDInsight-clusters:

* [Een HDInsight-cluster upgraden naar een nieuwere versie](../hdinsight-upgrade-cluster.md)
* [Azure HDInsight bewaken en beheren met de Apache Ambari Web UI](../hdinsight-hadoop-manage-ambari.md)
* [Apache Hadoop componenten en versies](../hdinsight-component-versioning.md)
* [Configuraties optimaliseren met Apache Ambari](../hdinsight-changing-configs-via-ambari.md#apache-hbase-optimization-with-the-ambari-web-ui)
