---
title: Gegevens kopiëren naar Azure Data Lake Storage Gen2 met DistCp| Microsoft Documenten
description: DistCp-tool gebruiken om gegevens van en naar Data Lake Storage Gen2 te kopiëren
author: normesta
ms.subservice: data-lake-storage-gen2
ms.service: storage
ms.topic: conceptual
ms.date: 12/06/2018
ms.author: normesta
ms.reviewer: stewu
ms.openlocfilehash: 3c09a95309e001def306698bbba4f6d0a1a2804d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79255533"
---
# <a name="use-distcp-to-copy-data-between-azure-storage-blobs-and-azure-data-lake-storage-gen2"></a>DistCp gebruiken om gegevens te kopiëren tussen Azure Storage Blobs en Azure Data Lake Storage Gen2

U [DistCp](https://hadoop.apache.org/docs/stable/hadoop-distcp/DistCp.html) gebruiken om gegevens te kopiëren tussen een V2-opslagaccount voor algemeen gebruik en een V2-opslagaccount voor algemeen gebruik met hiërarchische naamruimte ingeschakeld. In dit artikel vindt u instructies over het gebruik van het gereedschap DistCp.

DistCp biedt een verscheidenheid aan command-line parameters en we raden u ten zeerste aan om dit artikel te lezen om uw gebruik ervan te optimaliseren. In dit artikel wordt de basisfunctionaliteit weergegeven, terwijl de nadruk wordt verglasd op het gebruik ervan voor het kopiëren van gegevens naar een hiërarchische naamruimte-account.

## <a name="prerequisites"></a>Vereisten

* **Een Azure-abonnement**. Zie [Gratis proefversie van Azure ophalen](https://azure.microsoft.com/pricing/free-trial/).
* **Een bestaand Azure Storage-account zonder Data Lake Storage Gen2-mogelijkheden (hiërarchische naamruimte) ingeschakeld**.
* **Een Azure Storage-account met Data Lake Storage Gen2-functie ingeschakeld**. Zie [Een Azure Data Lake Storage Gen2-opslagaccount maken voor](data-lake-storage-quickstart-create-account.md) instructies over het maken van een azure data lake storage Gen2-opslagaccount
* **Een bestandssysteem** dat is gemaakt in het opslagaccount met hiërarchische naamruimte ingeschakeld.
* **Azure HDInsight-cluster** met toegang tot een opslagaccount met Data Lake Storage Gen2 ingeschakeld. Zie [Azure Data Lake Storage Gen2 gebruiken met Azure HDInsight-clusters](https://docs.microsoft.com/azure/hdinsight/hdinsight-hadoop-use-data-lake-storage-gen2?toc=%2fazure%2fstorage%2fblobs%2ftoc.json). Zorg ervoor dat u Extern bureaublad inschakelt voor het cluster.

## <a name="use-distcp-from-an-hdinsight-linux-cluster"></a>DistCp gebruiken vanuit een HDInsight Linux-cluster

Een HDInsight-cluster wordt geleverd met het DistCp-hulpprogramma, dat kan worden gebruikt om gegevens uit verschillende bronnen naar een HDInsight-cluster te kopiëren. Als u het HDInsight-cluster hebt geconfigureerd om Azure Blob Storage en Azure Data Lake Storage samen te gebruiken, kan het DistCp-hulpprogramma ook kant-en-klaar worden gebruikt om gegevens tussen te kopiëren. In deze sectie bekijken we hoe we het DistCp-hulpprogramma kunnen gebruiken.

1. Maak een SSH-sessie voor uw HDI-cluster. Zie [Verbinding maken met een HDInsight-cluster op Linux](../../hdinsight/hdinsight-hadoop-linux-use-ssh-unix.md).

2. Controleer of u toegang hebt tot uw bestaande V2-account voor algemene doeleinden (zonder dat de hiërarchische naamruimte is ingeschakeld).

        hdfs dfs –ls wasbs://<CONTAINER_NAME>@<STORAGE_ACCOUNT_NAME>.blob.core.windows.net/

    De uitvoer moet een lijst met inhoud in de container opleveren.

3. Controleer ook of u toegang hebt tot het opslagaccount met hiërarchische naamruimte die vanuit het cluster is ingeschakeld. Voer de volgende opdracht uit:

        hdfs dfs -ls abfss://<FILE_SYSTEM_NAME>@<STORAGE_ACCOUNT_NAME>.dfs.core.windows.net/

    De uitvoer moet een lijst met bestanden/mappen in het Data Lake Storage-account bevatten.

4. Gebruik DistCp om gegevens van WASB te kopiëren naar een Data Lake Storage-account.

        hadoop distcp wasbs://<CONTAINER_NAME>@<STORAGE_ACCOUNT_NAME>.blob.core.windows.net/example/data/gutenberg abfss://<FILE_SYSTEM_NAME>@<STORAGE_ACCOUNT_NAME>.dfs.core.windows.net/myfolder

    De opdracht kopieert de inhoud van de **map /voorbeeld/gegevens/gutenberg//map** in Blob-opslag naar **/myfolder** in het account Gegevensmeeropslag.

5. Gebruik op dezelfde manier DistCp om gegevens uit het Data Lake Storage-account naar Blob Storage (WASB) te kopiëren.

        hadoop distcp abfss://<FILE_SYSTEM_NAME>@<STORAGE_ACCOUNT_NAME>.dfs.core.windows.net/myfolder wasbs://<CONTAINER_NAME>@<STORAGE_ACCOUNT_NAME>.blob.core.windows.net/example/data/gutenberg

    De opdracht kopieert de inhoud van **/myfolder** in het Data Lake Store-account naar **/example/data/gutenberg/map** in WASB.

## <a name="performance-considerations-while-using-distcp"></a>Prestatieoverwegingen tijdens het gebruik van DistCp

Omdat de laagste granulariteit van DistCp één bestand is, is het instellen van het maximumaantal gelijktijdige kopieën de belangrijkste parameter om deze te optimaliseren ten opzichte van Data Lake Storage. Het aantal gelijktijdige kopieën is gelijk aan het aantal parameter mappers **(m)** op de opdrachtregel. Deze parameter geeft het maximum aantal mappers op dat wordt gebruikt om gegevens te kopiëren. Standaardwaarde is 20.

**Voorbeeld**

    hadoop distcp -m 100 wasbs://<CONTAINER_NAME>@<STORAGE_ACCOUNT_NAME>.blob.core.windows.net/example/data/gutenberg abfss://<FILE_SYSTEM_NAME>@<STORAGE_ACCOUNT_NAME>.dfs.core.windows.net/myfolder

### <a name="how-do-i-determine-the-number-of-mappers-to-use"></a>Hoe bepaal ik het aantal mappers dat moet worden gebruikt?

Hier volgen een aantal richtlijnen.

* **Stap 1: Bepaal het totale geheugen dat beschikbaar is voor de wachtrij voor de 'standaard' YARN-app** - De eerste stap is het bepalen van het geheugen dat beschikbaar is voor de wachtrij voor de 'standaard' YARN-app. Deze informatie is beschikbaar in de Ambari-portal die is gekoppeld aan het cluster. Navigeer naar YARN en bekijk het tabblad Configs om het YARN-geheugen te zien dat beschikbaar is voor de wachtrij voor 'standaard' apps. Dit is het totale beschikbare geheugen voor uw DistCp-taak (wat eigenlijk een mapreduce-taak is).

* **Stap 2: Bereken het aantal mappers** - De waarde van **m** is gelijk aan het quotiënt van het totale GARENgeheugen gedeeld door de grootte van de GARENcontainer. De YARN container formaat informatie is ook beschikbaar in de Ambari portal. Navigeer naar YARN en bekijk het tabblad Configs. De grootte van de YARN-container wordt in dit venster weergegeven. De vergelijking om te komen tot het aantal mappers **(m)** is

        m = (number of nodes * YARN memory for each node) / YARN container size

**Voorbeeld**

Laten we aannemen dat u een 4x D14v2s-cluster hebt en dat u probeert 10 TB aan gegevens over te dragen uit 10 verschillende mappen. Elk van de mappen bevat verschillende hoeveelheden gegevens en de bestandsgroottes binnen elke map zijn verschillend.

* **Totaal GAREN geheugen:** Uit de Ambari portal bepaalt u dat het YARN-geheugen 96 GB is voor een D14-knooppunt. Dus, totale GAREN geheugen voor vier knooppunt cluster is: 

        YARN memory = 4 * 96GB = 384GB

* **Aantal mappers**: Uit de Ambari-portal bepaalt u dat de grootte van de YARN-container 3.072 MB is voor een D14-clusterknooppunt. Dus, aantal mappers is:

        m = (4 nodes * 96GB) / 3072MB = 128 mappers

Als andere toepassingen geheugen gebruiken, u ervoor kiezen om slechts een deel van het YARN-geheugen van uw cluster te gebruiken voor DistCp.

### <a name="copying-large-datasets"></a>Grote gegevenssets kopiëren

Wanneer de grootte van de te verplaatsen gegevensset groot is (bijvoorbeeld >1 TB) of als u veel verschillende mappen hebt, moet u overwegen meerdere DistCp-taken te gebruiken. Er is waarschijnlijk geen prestatiewinst, maar het verspreidt de taken, zodat als een taak mislukt, hoeft u alleen die specifieke taak opnieuw op te starten in plaats van de hele taak.

### <a name="limitations"></a>Beperkingen

* DistCp probeert mappers te maken die qua grootte vergelijkbaar zijn om de prestaties te optimaliseren. Het verhogen van het aantal mappers kan niet altijd de prestaties verhogen.

* DistCp is beperkt tot slechts één mapper per bestand. Daarom moet je niet meer mappers hebben dan je bestanden hebt. Aangezien DistCp slechts één mapper aan een bestand kan toewijzen, beperkt dit de hoeveelheid gelijktijdigheid die kan worden gebruikt om grote bestanden te kopiëren.

* Als u een klein aantal grote bestanden hebt, moet u ze opsplitsen in bestandenbrokken van 256 MB om u meer potentiële gelijktijdigheid te geven.
