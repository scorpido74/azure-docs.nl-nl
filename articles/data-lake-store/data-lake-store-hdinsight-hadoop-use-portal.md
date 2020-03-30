---
title: Azure HDInsight-clusters maken met Data Lake Storage Gen1 - portal
description: Gebruik de Azure-portal om HDInsight-clusters te maken en te gebruiken met Azure Data Lake Storage Gen1
author: twooley
ms.service: data-lake-store
ms.topic: conceptual
ms.date: 05/29/2018
ms.author: twooley
ms.openlocfilehash: 1d1368ef8ffb474c6bec1240f567f043961597fb
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79265569"
---
# <a name="create-hdinsight-clusters-with-azure-data-lake-storage-gen1-by-using-the-azure-portal"></a>HDInsight-clusters maken met Azure Data Lake Storage Gen1 met behulp van de Azure-portal

> [!div class="op_single_selector"]
> * [De Azure-portal gebruiken](data-lake-store-hdinsight-hadoop-use-portal.md)
> * [PowerShell gebruiken (voor standaardopslag)](data-lake-store-hdinsight-hadoop-use-powershell-for-default-storage.md)
> * [PowerShell gebruiken (voor extra opslag)](data-lake-store-hdinsight-hadoop-use-powershell.md)
> * [Resourcebeheer gebruiken](data-lake-store-hdinsight-hadoop-use-resource-manager-template.md)
>
>

Meer informatie over het gebruik van de Azure-portal om een HDInsight-cluster te maken met een Azure Data Lake Storage Gen1-account als standaardopslag of extra opslag. Hoewel extra opslag optioneel is voor een HDInsight-cluster, is het raadzaam om uw bedrijfsgegevens op te slaan in de extra opslagaccounts.

## <a name="prerequisites"></a>Vereisten

Voordat u begint, moet u ervoor zorgen dat u aan de volgende vereisten hebt voldaan:

* **Een Azure-abonnement**. Ga naar [Gratis proefversie van Azure.](https://azure.microsoft.com/pricing/free-trial/)
* **Een Data Lake Storage Gen1-account**. Volg de instructies van [Aan de slag met Azure Data Lake Storage Gen1 met behulp van de Azure-portal.](data-lake-store-get-started-portal.md) U moet ook een hoofdmap voor het account maken.  In dit artikel wordt een hoofdmap met de naam __/clusters__ gebruikt.
* **Een Azure Active Directory-serviceprincipal**. Deze handleiding bevat instructies voor het maken van een serviceprincipal in Azure Active Directory (Azure AD). Als u echter een serviceprincipal wilt maken, moet u een Azure AD-beheerder zijn. Als u een beheerder bent, u deze vereiste overslaan en doorgaan.

>[!NOTE]
>U een serviceprincipal alleen maken als u een Azure AD-beheerder bent. Uw Azure AD-beheerder moet een serviceprincipal maken voordat u een HDInsight-cluster maken met Data Lake Storage Gen1. Ook moet de serviceprincipal worden gemaakt met een certificaat, zoals beschreven bij [Een serviceprincipal maken met certificaat](../active-directory/develop/howto-authenticate-service-principal-powershell.md#create-service-principal-with-self-signed-certificate).
>

## <a name="create-an-hdinsight-cluster"></a>Een HDInsight-cluster maken

In deze sectie maakt u een HDInsight-cluster met Data Lake Storage Gen1-accounts als standaard- of extra opslag. Dit artikel richt zich alleen op het gedeelte van het configureren van Data Lake Storage Gen1-accounts. Zie [Hadoop-clusters maken in HDInsight](../hdinsight/hdinsight-hadoop-provision-linux-clusters.md)voor de algemene clustercreatieinformatie en -procedures.

### <a name="create-a-cluster-with-data-lake-storage-gen1-as-default-storage"></a>Een cluster maken met Data Lake Storage Gen1 als standaardopslag

Ga als u een HDInsight-cluster maakt met een Data Lake Storage Gen1-account als standaardopslagaccount:

1. Meld u aan bij [Azure Portal](https://portal.azure.com).
2. Volg [Clusters maken](../hdinsight/hdinsight-hadoop-create-linux-clusters-portal.md#create-clusters) voor de algemene informatie over het maken van HDInsight-clusters.
3. Selecteer op het **opslagblad** onder **primaire opslagtype** **Azure Data Lake Storage Gen1**en voer vervolgens de volgende gegevens in:

    ![Serviceprincipal toevoegen aan het HDInsight-cluster](./media/data-lake-store-hdinsight-hadoop-use-portal/hdi.adl.1.adls.storage.png "Serviceprincipal toevoegen aan het HDInsight-cluster")

    * **Selecteer Data Lake Store-account:** selecteer een bestaand Data Lake Storage Gen1-account. Een bestaand Data Lake Storage Gen1-account is vereist.  Zie [Voorwaarden](#prerequisites).
    * **Hoofdpad:** voer een pad in waar de clusterspecifieke bestanden moeten worden opgeslagen. Op de screenshot, het is __/ clusters / myhdiadlcluster /__, waarin de / __clusters__ map moet bestaan, en de Portal maakt *myhdicluster* map.  De *myhdicluster* is de clusternaam.
    * **Data Lake Store-toegang**: Configureer de toegang tussen het Data Lake Storage Gen1-account en het HDInsight-cluster. Zie Toegang [tot Gegevensmeeropslag Gen1 configureren](#configure-data-lake-storage-gen1-access)voor instructies .
    * **Extra opslagaccounts:** Voeg Azure-opslagaccounts toe als extra opslagaccounts voor het cluster. Als u extra Data Lake Storage Gen1-accounts wilt toevoegen, wordt dit gedaan door de clustermachtigingen voor gegevens in meer Data Lake Storage Gen1-accounts te geven terwijl u een Data Lake Storage Gen1-account configureert als het primaire opslagtype. Zie [Toegang tot Data Lake Storage Gen1 configureren](#configure-data-lake-storage-gen1-access).

4. Klik in de toegang tot de **Data Lake Store**op **Selecteren**en ga vervolgens verder met clustercreatie zoals beschreven in [Hadoop-clusters maken in HDInsight](../hdinsight/hdinsight-hadoop-create-linux-clusters-portal.md).

### <a name="create-a-cluster-with-data-lake-storage-gen1-as-additional-storage"></a>Een cluster maken met Data Lake Storage Gen1 als extra opslag

Met de volgende instructies wordt een HDInsight-cluster gemaakt met een Azure-opslagaccount als standaardopslag en een Data Lake Storage Gen1-account als extra opslag.

Ga als extra opslagaccount met een Data Lake Storage Gen1-account naar een HDInsight-cluster met een Data Lake Storage Gen1-account:

1. Meld u aan bij [Azure Portal](https://portal.azure.com).
2. Volg [Clusters maken](../hdinsight/hdinsight-hadoop-create-linux-clusters-portal.md#create-clusters) voor de algemene informatie over het maken van HDInsight-clusters.
3. Selecteer **Azure Storage**onder het **blad Opslag** onder Primaire **opslag**en voer de volgende gegevens in:

    ![Serviceprincipal toevoegen aan het HDInsight-cluster](./media/data-lake-store-hdinsight-hadoop-use-portal/hdi.adl.1.png "Serviceprincipal toevoegen aan het HDInsight-cluster")

    * **Selectiemethode** : als u een opslagaccount wilt opgeven dat deel uitmaakt van uw Azure-abonnement, selecteert u **Mijn abonnementen**en selecteert u vervolgens het opslagaccount. Als u een opslagaccount wilt opgeven dat buiten uw Azure-abonnement valt, selecteert u **Access-sleutel**en geeft u de informatie voor het externe opslagaccount op.

    * **Standaardcontainer** - Gebruik de standaardwaarde of geef uw eigen naam op.
    * **Extra opslagaccounts** - Voeg meer Azure-opslagaccounts toe als extra opslag.
    * **Data Lake Store-toegang** - Toegang configureren tussen het Data Lake Storage Gen1-account en het HDInsight-cluster. Zie Toegang [tot Data Lake Storage Gen1 configureren](#configure-data-lake-storage-gen1-access)voor instructies .

## <a name="configure-data-lake-storage-gen1-access"></a>Toegang tot Data Lake Storage Gen1 configureren

In deze sectie configureert u Data Lake Storage Gen1-toegang vanuit HDInsight-clusters met behulp van een Azure Active Directory-serviceprincipal.

### <a name="specify-a-service-principal"></a>Een serviceprincipal opgeven

Vanuit de Azure-portal u een bestaande serviceprincipal gebruiken of een nieuwe portal maken.

Ga als lid van de Azure-portal naar een serviceprincipal:

1. Selecteer **Toegang tot de Data Lake Store** in het opslagblad.
1. Selecteer op het **toegangsblad datalakestorage Gen1** de optie **Nieuw maken**.
1. Selecteer **Serviceprincipal**en volg de instructies om een serviceprincipal te maken.
1. Download het certificaat als u besluit het in de toekomst opnieuw te gebruiken. Het downloaden van het certificaat is handig als u dezelfde serviceprincipal wilt gebruiken wanneer u extra HDInsight-clusters maakt.

    ![Serviceprincipal toevoegen aan het HDInsight-cluster](./media/data-lake-store-hdinsight-hadoop-use-portal/hdi.adl.2.png "Serviceprincipal toevoegen aan het HDInsight-cluster")

1. Selecteer **Access** om de maptoegang te configureren.  Zie [Bestandsmachtigingen configureren](#configure-file-permissions).

Ga als u een bestaande serviceprincipal van de Azure-portal gaat gebruiken:

1. Selecteer **toegang tot de Data Lake Store**.
1. Selecteer op het **toegangsblad datalakestorage Gen1** **bestaande .**
1. Selecteer **Serviceprincipal**en selecteer vervolgens een serviceprincipal.
1. Upload het certificaat (.pfx-bestand) dat is gekoppeld aan de geselecteerde serviceprincipal en voer vervolgens het certificaatwachtwoord in.

    ![Serviceprincipal toevoegen aan het HDInsight-cluster](./media/data-lake-store-hdinsight-hadoop-use-portal/hdi.adl.5.png "Serviceprincipal toevoegen aan het HDInsight-cluster")

1. Selecteer **Access** om de maptoegang te configureren.  Zie [Bestandsmachtigingen configureren](#configure-file-permissions).

### <a name="configure-file-permissions"></a><a name="configure-file-permissions"></a>Bestandsmachtigingen configureren

De configuratie is anders, afhankelijk van of het account wordt gebruikt als de standaardopslag of een extra opslagaccount:

* Wordt gebruikt als standaardopslag

  * toestemming op het basisniveau van het Data Lake Storage Gen1-account
  * toestemming op het hoofdniveau van de HDInsight-clusteropslag. Bijvoorbeeld de __map /clusters__ die eerder in de zelfstudie is gebruikt.

* Gebruiken als extra opslag

  * Toestemming bij de mappen waar u bestandstoegang nodig hebt.

Ga als het gaat om toestemming toe te wijzen op het hoofdniveau van het Data Lake Storage Gen1-account:

1. Selecteer **Access**op het **toegangsblad van Data Lake Storage Gen1** . Het blad **Voor bestandsmachtigingen selecteren** wordt geopend. Het bevat alle Data Lake Storage Gen1-accounts in uw abonnement.
1. Plaats de muis (klik niet) met de muis over de naam van het Data Lake Storage Gen1-account om het selectievakje zichtbaar te maken en schakel het selectievakje in.

    ![Serviceprincipal toevoegen aan het HDInsight-cluster](./media/data-lake-store-hdinsight-hadoop-use-portal/hdi.adl.3.png "Serviceprincipal toevoegen aan het HDInsight-cluster")

   __Lees,__ __SCHRIJF__en __UITVOEREN__ zijn standaard geselecteerd.

1. Klik op **Selecteren** onder aan de pagina.
1. Selecteer **Uitvoeren** om toestemming toe te wijzen.
1. Selecteer **Done**.

Ga als een overzicht van de machtiging op het hoofdniveau van het HDInsight-cluster:

1. Selecteer **Access**op het **toegangsblad van Data Lake Storage Gen1** . Het blad **Voor bestandsmachtigingen selecteren** wordt geopend. Het bevat alle Data Lake Storage Gen1-accounts in uw abonnement.
1. Selecteer in het blad **Bestandsmachtigingen selecteren** de naam van het Gen1-account gegevensarchief Gegevensmeeropslag om de inhoud weer te geven.
1. Schakel de hoofdmap voor clusteropslag HDInsight in door het selectievakje links van de map in te schakelen. Volgens de screenshot eerder, de cluster opslag root is __/ clusters__ map die u hebt opgegeven, terwijl het selecteren van Data Lake Storage Gen1 als standaard opslag.
1. Stel de machtigingen in op de map.  Standaard zijn lezen, schrijven en uitvoeren geselecteerd.
1. Klik op **Selecteren** onder aan de pagina.
1. Selecteer **Uitvoeren**.
1. Selecteer **Done**.

Als u Data Lake Storage Gen1 als extra opslag gebruikt, moet u alleen toestemming toewijzen voor de mappen die u vanuit het HDInsight-cluster wilt openen. In de onderstaande schermafbeelding geeft u bijvoorbeeld alleen toegang tot de map **mynewfolder** in een Data Lake Storage Gen1-account.

![Serviceprincipal-machtigingen toewijzen aan het HDInsight-cluster](./media/data-lake-store-hdinsight-hadoop-use-portal/hdi.adl.3-1.png "Serviceprincipal-machtigingen toewijzen aan het HDInsight-cluster")

## <a name="verify-cluster-setup"></a><a name="verify-cluster-set-up"></a>Clusterinstellingen verifiëren

Nadat de clusterinstelling is voltooid, controleert u op het clusterblad uw resultaten door een van de volgende stappen uit te voeren:

* Als u wilt controleren of de bijbehorende opslag voor het cluster het Door u opgegeven Gen1-account Data Lake Storage Gen1 is, selecteert u **Opslagaccounts** in het linkerdeelvenster.

    ![Serviceprincipal toevoegen aan het HDInsight-cluster](./media/data-lake-store-hdinsight-hadoop-use-portal/hdi.adl.6-1.png "Serviceprincipal toevoegen aan het HDInsight-cluster")

* Als u wilt controleren of de serviceprincipal correct is gekoppeld aan het HDInsight-cluster, selecteert u **Gegevensmeeropslag Gen1-toegang** in het linkerdeelvenster.

    ![Serviceprincipal toevoegen aan het HDInsight-cluster](./media/data-lake-store-hdinsight-hadoop-use-portal/hdi.adl.6.png "Serviceprincipal toevoegen aan het HDInsight-cluster")

## <a name="examples"></a>Voorbeelden

Nadat u het cluster hebt ingesteld met Data Lake Storage Gen1 als opslag, raadpleegt u deze voorbeelden van het gebruik van het HDInsight-cluster om de gegevens te analyseren die zijn opgeslagen in Data Lake Storage Gen1.

### <a name="run-a-hive-query-against-data-in-a-data-lake-storage-gen1-account-as-primary-storage"></a>Een Hive-query uitvoeren op basis van gegevens in een Data Lake Storage Gen1-account (als primaire opslag)

Als u een Hive-query wilt uitvoeren, gebruikt u de interface voor hive-weergaven in de Ambari-portal. Zie [De Hive View gebruiken met Hadoop in HDInsight](../hdinsight/hadoop/apache-hadoop-use-hive-ambari-view.md)voor instructies over het gebruik van Ambari Hive-weergaven.

Wanneer u met gegevens werkt in een Data Lake Storage Gen1-account, moeten er een paar tekenreeksen worden gewijzigd.

Als u bijvoorbeeld het cluster gebruikt dat u hebt gemaakt met Data Lake Storage Gen1 als primaire opslag, is het pad naar de gegevens: *adl://<data_lake_storage_gen1_account_name>/azuredatalakestore.net/path/to/file*. Een Hive-query om een tabel te maken op basis van voorbeeldgegevens die zijn opgeslagen in het Data Lake Storage Gen1-account, lijkt op de volgende instructie:

    CREATE EXTERNAL TABLE websitelog (str string) LOCATION 'adl://hdiadlsg1storage.azuredatalakestore.net/clusters/myhdiadlcluster/HdiSamples/HdiSamples/WebsiteLogSampleData/SampleLog/'

Beschrijvingen:

* `adl://hdiadlsg1storage.azuredatalakestore.net/`is de basis van het Data Lake Storage Gen1-account.
* `/clusters/myhdiadlcluster`is de hoofdmap van de clustergegevens die u hebt opgegeven tijdens het maken van het cluster.
* `/HdiSamples/HdiSamples/WebsiteLogSampleData/SampleLog/`is de locatie van het voorbeeldbestand dat u in de query hebt gebruikt.

### <a name="run-a-hive-query-against-data-in-a-data-lake-storage-gen1-account-as-additional-storage"></a>Een Hive-query uitvoeren op basis van gegevens in een Data Lake Storage Gen1-account (als extra opslag)

Als het gecluster dat u hebt gemaakt Blob-opslag gebruikt als standaardopslag, worden de voorbeeldgegevens niet opgenomen in het Data Lake Storage Gen1-account dat wordt gebruikt als extra opslag. Breng in een dergelijk geval eerst de gegevens van Blob-opslag over naar het Data Lake Storage Gen1-account en voer vervolgens de query's uit zoals weergegeven in het voorgaande voorbeeld.

Zie de volgende artikelen voor informatie over het kopiëren van gegevens uit Blob-opslag naar een Data Lake Storage Gen1-account:

* [Distcp gebruiken om gegevens te kopiëren tussen Azure Storage-blobs en Data Lake Storage Gen1](data-lake-store-copy-data-wasb-distcp.md)
* [AdlCopy gebruiken om gegevens van Azure Storage-blobs naar Data Lake Storage Gen1 te kopiëren](data-lake-store-copy-data-azure-storage-blob.md)

### <a name="use-data-lake-storage-gen1-with-a-spark-cluster"></a>Data Lake Storage Gen1 gebruiken met een Spark-cluster

U een Spark-cluster gebruiken om Spark-taken uit te voeren op gegevens die zijn opgeslagen in een Data Lake Storage Gen1-account. Zie [HDInsight Spark-cluster gebruiken om gegevens te analyseren in Data Lake Storage Gen1](../hdinsight/spark/apache-spark-use-with-data-lake-store.md)voor meer informatie.

### <a name="use-data-lake-storage-gen1-in-a-storm-topology"></a>Data Lake Storage Gen1 gebruiken in een stormtopologie

U het Data Lake Storage Gen1-account gebruiken om gegevens te schrijven van een Storm-topologie. Zie Azure Data Lake Storage [Gen1 gebruiken met Apache Storm met HDInsight](../hdinsight/storm/apache-storm-write-data-lake-store.md)voor instructies over het bereiken van dit scenario.

## <a name="see-also"></a>Zie ook

* [Data Lake Storage Gen1 gebruiken met Azure HDInsight-clusters](../hdinsight/hdinsight-hadoop-use-data-lake-store.md)
* [PowerShell: maak een HDInsight-cluster om Data Lake Storage Gen1 te gebruiken](data-lake-store-hdinsight-hadoop-use-powershell.md)

[makecert]: https://msdn.microsoft.com/library/windows/desktop/ff548309(v=vs.85).aspx
[pvk2pfx]: https://msdn.microsoft.com/library/windows/desktop/ff550672(v=vs.85).aspx
