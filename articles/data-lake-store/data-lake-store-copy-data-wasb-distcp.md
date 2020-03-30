---
title: Gegevens van en naar WASB kopiëren naar Azure Data Lake Storage Gen1 met DistCp
description: Het gereedschap DistCp gebruiken om gegevens van en naar Azure Storage-blobs naar Azure Data Lake Storage Gen1 te kopiëren
author: twooley
ms.service: data-lake-store
ms.topic: conceptual
ms.date: 01/03/2020
ms.author: twooley
ms.openlocfilehash: 455e73ece2d46a508b3077c13c8106fe53beb4de
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75638830"
---
# <a name="use-distcp-to-copy-data-between-azure-storage-blobs-and-azure-data-lake-storage-gen1"></a>DistCp gebruiken om gegevens te kopiëren tussen Azure Storage blobs en Azure Data Lake Storage Gen1

> [!div class="op_single_selector"]
> * [DistCp gebruiken](data-lake-store-copy-data-wasb-distcp.md)
> * [AdlCopy gebruiken](data-lake-store-copy-data-azure-storage-blob.md)
>
>

Als u een HDInsight-cluster hebt met toegang tot Azure Data Lake Storage Gen1, u Hadoop-ecosysteemtools zoals DistCp gebruiken om gegevens van en naar een HDInsight-clusteropslag (WASB) naar een Data Lake Storage Gen1-account te kopiëren. In dit artikel ziet u hoe u het gereedschap DistCp gebruiken.

## <a name="prerequisites"></a>Vereisten

* **Een Azure-abonnement**. Zie [Gratis proefversie van Azure ophalen](https://azure.microsoft.com/pricing/free-trial/).
* **Een Azure Data Lake Storage Gen1-account**. Zie [Aan de slag met Azure Data Lake Storage Gen1](data-lake-store-get-started-portal.md)voor instructies over het maken van een account.
* **Azure HDInsight-cluster** met toegang tot een Data Lake Storage Gen1-account. Zie [Een HDInsight-cluster maken met Data Lake Storage Gen1](data-lake-store-hdinsight-hadoop-use-portal.md). Zorg ervoor dat u Extern bureaublad inschakelt voor het cluster.

## <a name="use-distcp-from-an-hdinsight-linux-cluster"></a>DistCp gebruiken vanuit een HDInsight Linux-cluster

Een HDInsight-cluster wordt geleverd met de DistCp-tool, die kan worden gebruikt om gegevens uit verschillende bronnen naar een HDInsight-cluster te kopiëren. Als u het HDInsight-cluster hebt geconfigureerd om Data Lake Storage Gen1 als extra opslag te gebruiken, u DistCp out-of-the-box gebruiken om gegevens van en naar een Data Lake Storage Gen1-account te kopiëren. In deze sectie bekijken we hoe we de DistCp-tool kunnen gebruiken.

1. Gebruik SSH vanaf uw bureaublad om verbinding te maken met het cluster. Zie [Verbinding maken met een HDInsight-cluster op Linux](../hdinsight/hdinsight-hadoop-linux-use-ssh-unix.md). Voer de opdrachten uit de SSH-prompt.

1. Controleer of u toegang hebt tot de Azure Storage-blobs (WASB). Voer de volgende opdracht uit:

   ```
   hdfs dfs –ls wasb://<container_name>@<storage_account_name>.blob.core.windows.net/
   ```

   De uitvoer bevat een lijst met inhoud in de opslagblob.

1. Controleer ook of u vanuit het cluster toegang hebt tot het Gen1-account van Data Lake Storage. Voer de volgende opdracht uit:

   ```
   hdfs dfs -ls adl://<data_lake_storage_gen1_account>.azuredatalakestore.net:443/
   ```

    De uitvoer bevat een lijst met bestanden en mappen in het Data Lake Storage Gen1-account.

1. Gebruik DistCp om gegevens van WASB te kopiëren naar een Data Lake Storage Gen1-account.

   ```
   hadoop distcp wasb://<container_name>@<storage_account_name>.blob.core.windows.net/example/data/gutenberg adl://<data_lake_storage_gen1_account>.azuredatalakestore.net:443/myfolder
   ```

    De opdracht kopieert de inhoud van de **map /example/data/gutenberg//in** WASB naar **/myfolder** in het Data Lake Storage Gen1-account.

1. Gebruik op dezelfde manier DistCp om gegevens van een Data Lake Storage Gen1-account naar WASB te kopiëren.

   ```
   hadoop distcp adl://<data_lake_storage_gen1_account>.azuredatalakestore.net:443/myfolder wasb://<container_name>@<storage_account_name>.blob.core.windows.net/example/data/gutenberg
   ```

    De opdracht kopieert de inhoud van **/myfolder** in het Data Lake Storage Gen1-account naar **/example/data/gutenberg/map** in WASB.

## <a name="performance-considerations-while-using-distcp"></a>Prestatieoverwegingen tijdens het gebruik van DistCp

Omdat de laagste granulariteit van het gereedschap DistCp één bestand is, is het instellen van het maximumaantal gelijktijdige kopieën de belangrijkste parameter om het te optimaliseren ten opzichte van Data Lake Storage Gen1. U het aantal gelijktijdige kopieën bepalen door het aantal parameter mappers ('m') in te stellen op de opdrachtregel. Deze parameter geeft het maximum aantal mappers op dat wordt gebruikt om gegevens te kopiëren. De standaardwaarde is 20.

Voorbeeld:

```
 hadoop distcp wasb://<container_name>@<storage_account_name>.blob.core.windows.net/example/data/gutenberg adl://<data_lake_storage_gen1_account>.azuredatalakestore.net:443/myfolder -m 100
```

### <a name="how-to-determine-the-number-of-mappers-to-use"></a>Hoe bepaal je het aantal mappers dat je moet gebruiken?

Hier volgen een aantal richtlijnen.

* **Stap 1: Bepaal het totale GAREN-geheugen** - De eerste stap is het bepalen van het YARN-geheugen dat beschikbaar is voor het cluster waar u de distCp-taak uitvoert. Deze informatie is beschikbaar in de Ambari-portal die is gekoppeld aan het cluster. Navigeer naar YARN en bekijk het tabblad **Configs** om het YARN-geheugen te bekijken. Om het totale YARN-geheugen te krijgen, vermenigvuldigt u het YARN-geheugen per knooppunt met het aantal knooppunten dat u in uw cluster hebt.

* **Stap 2: Bereken het aantal mappers** - De waarde van **m** is gelijk aan het quotiënt van het totale GARENgeheugen gedeeld door de grootte van de GARENcontainer. De YARN container formaat informatie is ook beschikbaar in de Ambari portal. Navigeer naar YARN en bekijk het tabblad **Configs.** De grootte van de YARN-container wordt in dit venster weergegeven. De vergelijking om te komen tot het aantal mappers **(m)** is:

   `m = (number of nodes * YARN memory for each node) / YARN container size`

Voorbeeld:

Stel dat u vier D14v2s-knooppunten in het cluster hebt en dat u 10 TB aan gegevens uit 10 verschillende mappen wilt overbrengen. Elk van de mappen bevat verschillende hoeveelheden gegevens en de bestandsgroottes binnen elke map zijn verschillend.

* Totaal GAREN-geheugen - Uit de Ambari-portal bepaalt u dat het YARN-geheugen 96 GB is voor een D14-knooppunt. Dus, totale GAREN geheugen voor vier knooppunt cluster is:

   `YARN memory = 4 * 96GB = 384GB`

* Aantal mappers - Uit de Ambari portal bepaalt u dat de YARN-containergrootte 3072 is voor een D14-clusterknooppunt. Dus, het aantal mappers is:

   `m = (4 nodes * 96GB) / 3072MB = 128 mappers`

Als andere toepassingen geheugen gebruiken, u ervoor kiezen om slechts een deel van het YARN-geheugen van uw cluster te gebruiken voor DistCp.

### <a name="copying-large-datasets"></a>Grote gegevenssets kopiëren

Wanneer de grootte van de te verplaatsen gegevensset groot is (bijvoorbeeld > 1 TB) of als u veel verschillende mappen hebt, u overwegen meerdere DistCp-taken te gebruiken. Er is waarschijnlijk geen prestatiewinst, maar het verspreidt de taken, zodat als een taak mislukt, u die specifieke taak alleen opnieuw moet starten in plaats van de hele taak.

### <a name="limitations"></a>Beperkingen

* DistCp probeert mappers te maken die qua grootte vergelijkbaar zijn om de prestaties te optimaliseren. Het verhogen van het aantal mappers kan niet altijd de prestaties verhogen.

* DistCp is beperkt tot slechts één mapper per bestand. Daarom moet je niet meer mappers hebben dan je bestanden hebt. Omdat DistCp slechts één mapper aan een bestand kan toewijzen, beperkt dit de hoeveelheid gelijktijdigheid die kan worden gebruikt om grote bestanden te kopiëren.

* Als u een klein aantal grote bestanden hebt, splitst u ze op in bestandsbrokken van 256 MB om u meer potentiële gelijktijdigheid te geven.

* Als u kopieert vanuit een Azure Blob-opslagaccount, wordt uw kopieertaak mogelijk beperkt aan de opslagzijde van de Blob. Dit degradeert de prestaties van uw kopieerwerk. Zie Azure Storage-limieten bij [Azure-abonnements- en servicelimieten](../azure-resource-manager/management/azure-subscription-service-limits.md)voor meer informatie over de limieten van Azure Blob-opslag.

## <a name="see-also"></a>Zie ook

* [Gegevens uit Azure Storage-blobs kopiëren naar Data Lake Storage Gen1](data-lake-store-copy-data-azure-storage-blob.md)
* [Gegevens beveiligen in Data Lake Storage Gen1](data-lake-store-secure-data.md)
* [Azure Data Lake Analytics gebruiken met Data Lake Storage Gen1](../data-lake-analytics/data-lake-analytics-get-started-portal.md)
* [Azure HDInsight gebruiken met Data Lake Storage Gen1](data-lake-store-hdinsight-hadoop-use-portal.md)
