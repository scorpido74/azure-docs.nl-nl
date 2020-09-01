---
title: Azure HDInsight-clusters maken met Data Lake Storage Gen1-Portal
description: Gebruik de Azure Portal om HDInsight-clusters te maken en gebruiken met Azure Data Lake Storage Gen1
author: twooley
ms.service: data-lake-store
ms.topic: how-to
ms.date: 05/29/2018
ms.author: twooley
ms.openlocfilehash: fd49ddcb59e0d0f3a706f566cf0c011116b1501a
ms.sourcegitcommit: d68c72e120bdd610bb6304dad503d3ea89a1f0f7
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 09/01/2020
ms.locfileid: "89229222"
---
# <a name="create-hdinsight-clusters-with-azure-data-lake-storage-gen1-by-using-the-azure-portal"></a>Maak HDInsight-clusters met Azure Data Lake Storage Gen1 met behulp van de Azure Portal

> [!div class="op_single_selector"]
> * [De Azure-portal gebruiken](data-lake-store-hdinsight-hadoop-use-portal.md)
> * [Power shell gebruiken (voor standaard opslag)](data-lake-store-hdinsight-hadoop-use-powershell-for-default-storage.md)
> * [Power shell gebruiken (voor extra opslag)](data-lake-store-hdinsight-hadoop-use-powershell.md)
> * [Resource Manager gebruiken](data-lake-store-hdinsight-hadoop-use-resource-manager-template.md)
>
>

Meer informatie over het gebruik van de Azure Portal voor het maken van een HDInsight-cluster met een Azure Data Lake Storage Gen1-account als de standaard opslag of extra opslag. Hoewel extra opslag optioneel is voor een HDInsight-cluster, is het raadzaam om uw zakelijke gegevens op te slaan in de extra opslag accounts.

## <a name="prerequisites"></a>Vereisten

Voordat u begint, moet u ervoor zorgen dat u aan de volgende vereisten voldoet:

* **Een Azure-abonnement**. Ga naar [gratis proef versie van Azure](https://azure.microsoft.com/pricing/free-trial/).
* **Een Data Lake Storage gen1-account**. Volg de instructies in aan [de slag met Azure data Lake Storage gen1 met behulp van de Azure Portal](data-lake-store-get-started-portal.md). U moet ook een hoofdmap maken op het account.  In dit artikel wordt een hoofdmap met de naam __/clusters__ gebruikt.
* **Een Azure Active Directory Service-Principal**. In deze hand leiding vindt u instructies voor het maken van een Service-Principal in Azure Active Directory (Azure AD). Als u een Service-Principal wilt maken, moet u echter een Azure AD-beheerder zijn. Als u een beheerder bent, kunt u deze vereiste overs Laan en door gaan.

>[!NOTE]
>U kunt alleen een service-principal maken als u een Azure AD-beheerder bent. Uw Azure AD-beheerder moet een service-principal maken voordat u met Data Lake Storage Gen1 een HDInsight-cluster kunt maken. Daarnaast moet de service-principal worden gemaakt met een certificaat, zoals wordt beschreven in [een service-principal maken met een certificaat](../active-directory/develop/howto-authenticate-service-principal-powershell.md#create-service-principal-with-self-signed-certificate).
>

## <a name="create-an-hdinsight-cluster"></a>Een HDInsight-cluster maken

In deze sectie maakt u een HDInsight-cluster met Data Lake Storage Gen1 accounts als de standaard of de extra opslag ruimte. In dit artikel wordt alleen aandacht besteed aan het configureren van Data Lake Storage Gen1-accounts. Zie [Hadoop-clusters maken in HDInsight](../hdinsight/hdinsight-hadoop-provision-linux-clusters.md)voor algemene informatie over het maken van clusters en procedures.

### <a name="create-a-cluster-with-data-lake-storage-gen1-as-default-storage"></a>Een cluster maken met Data Lake Storage Gen1 als standaard opslag

Een HDInsight-cluster met een Data Lake Storage Gen1-account maken als het standaard opslag account:

1. Meld u aan bij [Azure Portal](https://portal.azure.com).
2. Volg [clusters maken](../hdinsight/hdinsight-hadoop-create-linux-clusters-portal.md#create-clusters) voor algemene informatie over het maken van HDInsight-clusters.
3. Selecteer op de Blade **opslag** onder **primair opslag type** **Azure data Lake Storage gen1**en voer de volgende gegevens in:

    ![Service-Principal toevoegen aan HDInsight-cluster](./media/data-lake-store-hdinsight-hadoop-use-portal/hdi.adl.1.adls.storage.png "Service-Principal toevoegen aan HDInsight-cluster")

    * **Selecteer data Lake Store account**: Selecteer een bestaand data Lake Storage gen1-account. Er is een bestaand Data Lake Storage Gen1 account vereist.  Zie [Vereisten](#prerequisites).
    * **Pad naar hoofdmap**: Voer een pad in waar de cluster-specifieke bestanden moeten worden opgeslagen. Op de scherm opname is de __/clusters/myhdiadlcluster/__, waarin de __/clusters__ -map moet bestaan, en de portal maakt de map *myhdicluster* .  De *myhdicluster* is de cluster naam.
    * **Data Lake Store toegang**: Configureer de toegang tussen het data Lake Storage gen1-account en het HDInsight-cluster. Zie voor instructies [Data Lake Storage gen1 toegang configureren](#configure-data-lake-storage-gen1-access).
    * **Extra opslag accounts**: Voeg Azure Storage-accounts toe als extra opslag accounts voor het cluster. U kunt extra Data Lake Storage Gen1 accounts toevoegen door de cluster machtigingen te geven op gegevens in meer Data Lake Storage Gen1 accounts tijdens het configureren van een Data Lake Storage Gen1-account als primair opslag type. Zie [Data Lake Storage gen1 toegang configureren](#configure-data-lake-storage-gen1-access).

4. Klik op de **Data Lake Store toegang**op **selecteren**en ga vervolgens door met het maken van een cluster zoals beschreven in [Hadoop-clusters maken in HDInsight](../hdinsight/hdinsight-hadoop-create-linux-clusters-portal.md).

### <a name="create-a-cluster-with-data-lake-storage-gen1-as-additional-storage"></a>Een cluster maken met Data Lake Storage Gen1 als extra opslag

De volgende instructies maken een HDInsight-cluster met een Azure Storage-account als de standaard opslag en een Data Lake Storage Gen1-account als extra opslag.

Een HDInsight-cluster met een Data Lake Storage Gen1-account maken als een extra opslag account:

1. Meld u aan bij [Azure Portal](https://portal.azure.com).
2. Volg [clusters maken](../hdinsight/hdinsight-hadoop-create-linux-clusters-portal.md#create-clusters) voor algemene informatie over het maken van HDInsight-clusters.
3. Selecteer op de Blade **opslag** onder **primair opslag type** **Azure Storage**en voer de volgende gegevens in:

    ![Service-Principal toevoegen aan HDInsight-cluster](./media/data-lake-store-hdinsight-hadoop-use-portal/hdi.adl.1.png "Service-Principal toevoegen aan HDInsight-cluster")

    * **Selectie methode** : als u een opslag account wilt opgeven dat deel uitmaakt van uw Azure-abonnement, selecteert u **Mijn abonnementen**en selecteert u vervolgens het opslag account. Als u een opslag account wilt opgeven dat zich buiten uw Azure-abonnement bevindt, selecteert u **toegangs sleutel**en geeft u de informatie voor het externe opslag account op.

    * **Standaard container** : gebruik de standaard waarde of geef uw eigen naam op.
    * **Extra opslag accounts** : Voeg meer Azure-opslag accounts toe als extra opslag ruimte.
    * **Data Lake Store toegang** : Configureer de toegang tussen het data Lake Storage gen1-account en het HDInsight-cluster. Zie [configure data Lake Storage gen1 Access](#configure-data-lake-storage-gen1-access)(Engelstalig) voor instructies.

## <a name="configure-data-lake-storage-gen1-access"></a>Data Lake Storage Gen1 toegang configureren

In deze sectie configureert u Data Lake Storage Gen1 toegang vanaf HDInsight-clusters met behulp van een Azure Active Directory Service-Principal.

### <a name="specify-a-service-principal"></a>Een Service-Principal opgeven

Vanuit de Azure Portal, kunt u een bestaande Service-Principal gebruiken of een nieuwe maken.

Een service-principal maken op basis van de Azure Portal:
1. Zie [Service-Principal en certificaten maken](https://docs.microsoft.com/azure/active-directory/develop/howto-create-service-principal-portal) met behulp van Azure Active Directory.

Een bestaande Service-Principal gebruiken van de Azure Portal:

1. De Service-Principal moet eigenaars machtigingen hebben voor het opslag account. Zie [machtigingen voor de service-principal instellen als eigenaar van het opslag account](#configure-serviceprincipal-permissions)
1. Selecteer **Data Lake Store toegang**.
1. Selecteer op de Blade **Data Lake Storage gen1 toegang** de optie **bestaande gebruiken**.
1. Selecteer **Service-Principal**en selecteer vervolgens een service-principal.
1. Upload het certificaat (. pfx-bestand) dat is gekoppeld aan de geselecteerde service-principal en voer het certificaat wachtwoord in.

    ![Service-Principal toevoegen aan HDInsight-cluster](./media/data-lake-store-hdinsight-hadoop-use-portal/hdi.adl.5.png "Service-Principal toevoegen aan HDInsight-cluster")

1. Selecteer **toegang** om de toegang tot de map te configureren.  Zie [machtigingen voor bestanden configureren](#configure-file-permissions).

### <a name="set-up-permissions-for-the-service-principal-to-be-owner-on-the-storage-account"></a><a name="configure-serviceprincipal-permissions"></a>Stel de machtigingen voor de Service-Principal in op eigenaar van het opslag account
1. Klik op de Blade Access Control (IAM) van het opslag account op een roltoewijzing toevoegen. 
2. Op de Blade een roltoewijzing toevoegen selecteert u rol als eigenaar en selecteert u de SPN en klikt u op opslaan.

### <a name="configure-file-permissions"></a><a name="configure-file-permissions"></a>Bestands machtigingen configureren

De configuratie verschilt, afhankelijk van het feit of het account wordt gebruikt als de standaard opslag of een extra opslag account:

* Gebruikt als standaard opslag

  * machtiging op het hoofd niveau van het Data Lake Storage Gen1-account
  * machtiging op het hoofd niveau van de HDInsight-cluster opslag. De map __/clusters__ wordt bijvoorbeeld eerder in de zelf studie gebruikt.

* Gebruiken als extra opslag

  * Machtigingen op de mappen waar u toegang tot het bestand nodig hebt.

Om machtigingen toe te wijzen op het hoofd niveau van het Data Lake Storage Gen1-account:

1. Selecteer **toegang**op de blade **Data Lake Storage gen1 toegang** . De Blade **Bestands machtigingen selecteren** wordt geopend. Alle Data Lake Storage Gen1 accounts in uw abonnement worden weer gegeven.
1. Houd de muis aanwijzer boven de naam van het Data Lake Storage Gen1-account om het selectie vakje zichtbaar te maken en schakel vervolgens het selectie vakje in.

    ![Service-Principal toevoegen aan HDInsight-cluster](./media/data-lake-store-hdinsight-hadoop-use-portal/hdi.adl.3.png "Service-Principal toevoegen aan HDInsight-cluster")

   Standaard zijn __lezen__, __schrijven__en __uitvoeren__ geselecteerd.

1. Klik op **selecteren** onder aan de pagina.
1. Selecteer **uitvoeren** om machtigingen toe te wijzen.
1. Selecteer **Gereed**.

Machtigingen toewijzen op het basis niveau van het HDInsight-cluster:

1. Selecteer **toegang**op de blade **Data Lake Storage gen1 toegang** . De Blade **Bestands machtigingen selecteren** wordt geopend. Alle Data Lake Storage Gen1 accounts in uw abonnement worden weer gegeven.
1. Selecteer op de Blade **Bestands machtigingen selecteren** de naam van het data Lake Storage gen1-account om de inhoud ervan weer te geven.
1. Selecteer de hoofdmap HDInsight-cluster opslag door het selectie vakje aan de linkerkant van de map in te scha kelen. Volgens de scherm afbeelding eerder is de hoofdmap van de cluster opslag __/clusters__ map die u hebt opgegeven tijdens het selecteren van data Lake Storage gen1 als standaard opslag.
1. Stel de machtigingen voor de map in.  Standaard zijn lezen, schrijven en uitvoeren geselecteerd.
1. Klik op **selecteren** onder aan de pagina.
1. Selecteer **Uitvoeren**.
1. Selecteer **Gereed**.

Als u Data Lake Storage Gen1 als extra opslag gebruikt, moet u alleen machtigingen toewijzen voor de mappen die u wilt openen vanuit het HDInsight-cluster. In de onderstaande scherm afbeelding hebt u bijvoorbeeld alleen toegang tot de map **mynewfolder** in een Data Lake Storage gen1-account.

![Service-Principal-machtigingen toewijzen aan het HDInsight-cluster](./media/data-lake-store-hdinsight-hadoop-use-portal/hdi.adl.3-1.png "Service-Principal-machtigingen toewijzen aan het HDInsight-cluster")

## <a name="verify-cluster-setup"></a><a name="verify-cluster-set-up"></a>Cluster installatie controleren

Nadat het installeren van het cluster is voltooid, controleert u op de Blade cluster uw resultaten door een van de volgende stappen uit te voeren:

* Als u wilt controleren of de gekoppelde opslag voor het cluster het Data Lake Storage Gen1 account is dat u hebt opgegeven, selecteert u **opslag accounts** in het linkerdeel venster.

    ![Service-Principal toevoegen aan HDInsight-cluster](./media/data-lake-store-hdinsight-hadoop-use-portal/hdi.adl.6-1.png "Service-Principal toevoegen aan HDInsight-cluster")

* Als u wilt controleren of de Service-Principal correct is gekoppeld aan het HDInsight-cluster, selecteert u **Data Lake Storage gen1 toegang** in het linkerdeel venster.

    ![Service-Principal toevoegen aan HDInsight-cluster](./media/data-lake-store-hdinsight-hadoop-use-portal/hdi.adl.6.png "Service-Principal toevoegen aan HDInsight-cluster")

## <a name="examples"></a>Voorbeelden

Nadat u het cluster met Data Lake Storage Gen1 als uw opslag hebt ingesteld, raadpleegt u deze voor beelden van het gebruik van HDInsight-cluster om de gegevens te analyseren die zijn opgeslagen in Data Lake Storage Gen1.

### <a name="run-a-hive-query-against-data-in-a-data-lake-storage-gen1-account-as-primary-storage"></a>Een Hive-query uitvoeren op gegevens in een Data Lake Storage Gen1 account (als primaire opslag)

Als u een Hive-query wilt uitvoeren, gebruikt u de interface Hive-weer gaven in de Ambari-Portal. Zie [de Hive-weer gave gebruiken met Hadoop in HDInsight](../hdinsight/hadoop/apache-hadoop-use-hive-ambari-view.md)voor instructies over het gebruik van Ambari-Hive-weer gaven.

Wanneer u met gegevens in een Data Lake Storage Gen1 account werkt, zijn er enkele teken reeksen die u kunt wijzigen.

Als u bijvoorbeeld het cluster gebruikt dat u hebt gemaakt met Data Lake Storage Gen1 als primaire opslag, is het pad naar de gegevens: *adl://<data_lake_storage_gen1_account_name>/azuredatalakestore.net/Path/to/file*. Een Hive-query voor het maken van een tabel uit voorbeeld gegevens die zijn opgeslagen in het Data Lake Storage Gen1-account ziet er als volgt uit:

```console
CREATE EXTERNAL TABLE websitelog (str string) LOCATION 'adl://hdiadlsg1storage.azuredatalakestore.net/clusters/myhdiadlcluster/HdiSamples/HdiSamples/WebsiteLogSampleData/SampleLog/'
```

Beschreven

* `adl://hdiadlsg1storage.azuredatalakestore.net/` is de hoofdmap van het Data Lake Storage Gen1-account.
* `/clusters/myhdiadlcluster` is de basis van de cluster gegevens die u hebt opgegeven tijdens het maken van het cluster.
* `/HdiSamples/HdiSamples/WebsiteLogSampleData/SampleLog/` is de locatie van het voorbeeld bestand dat u in de query hebt gebruikt.

### <a name="run-a-hive-query-against-data-in-a-data-lake-storage-gen1-account-as-additional-storage"></a>Een Hive-query uitvoeren op gegevens in een Data Lake Storage Gen1 account (als extra opslag)

Als het cluster dat u hebt gemaakt, Blob Storage als standaard opslag gebruikt, worden de voorbeeld gegevens niet opgenomen in het Data Lake Storage Gen1-account dat wordt gebruikt als extra opslag. In dat geval moet u eerst de gegevens van de Blob-opslag overdragen naar het Data Lake Storage Gen1-account en vervolgens de query's uitvoeren zoals in het voor gaande voor beeld.

Raadpleeg de volgende artikelen voor meer informatie over het kopiëren van gegevens uit Blobopslag naar een Data Lake Storage Gen1-account:

* [Gebruik Distcp om gegevens te kopiëren tussen Azure Storage blobs en Data Lake Storage Gen1](data-lake-store-copy-data-wasb-distcp.md)
* [Gebruik AdlCopy om gegevens te kopiëren van Azure Storage-blobs naar Data Lake Storage Gen1](data-lake-store-copy-data-azure-storage-blob.md)

### <a name="use-data-lake-storage-gen1-with-a-spark-cluster"></a>Data Lake Storage Gen1 gebruiken met een Spark-cluster

U kunt een Spark-cluster gebruiken om Spark-taken uit te voeren op gegevens die zijn opgeslagen in een Data Lake Storage Gen1-account. Zie [HDInsight Spark-cluster gebruiken voor het analyseren van gegevens in data Lake Storage gen1](../hdinsight/spark/apache-spark-use-with-data-lake-store.md)voor meer informatie.

### <a name="use-data-lake-storage-gen1-in-a-storm-topology"></a>Data Lake Storage Gen1 gebruiken in een storm-topologie

U kunt het Data Lake Storage Gen1-account gebruiken om gegevens van een storm-topologie te schrijven. Zie [Azure data Lake Storage gen1 gebruiken met Apache Storm met HDInsight](../hdinsight/storm/apache-storm-write-data-lake-store.md)voor instructies over het uitvoeren van dit scenario.

## <a name="see-also"></a>Zie ook

* [Data Lake Storage Gen1 gebruiken met Azure HDInsight-clusters](../hdinsight/hdinsight-hadoop-use-data-lake-store.md)
* [Power shell: een HDInsight-cluster maken voor het gebruik van Data Lake Storage Gen1](data-lake-store-hdinsight-hadoop-use-powershell.md)

[makecert]: https://msdn.microsoft.com/library/windows/desktop/ff548309(v=vs.85).aspx
[pvk2pfx]: https://msdn.microsoft.com/library/windows/desktop/ff550672(v=vs.85).aspx
