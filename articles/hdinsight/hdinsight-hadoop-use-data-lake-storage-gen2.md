---
title: Azure Data Lake Storage Gen2 gebruiken met Azure HDInsight-clusters
description: Meer informatie over het gebruik van Azure Data Lake Storage Gen2 met Azure HDInsight-clusters.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.custom: hdinsightactive
ms.date: 02/20/2020
ms.openlocfilehash: d711cc7e58fb055eda62cfc364a5552a7d10f7bd
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79272290"
---
# <a name="use-azure-data-lake-storage-gen2-with-azure-hdinsight-clusters"></a>Azure Data Lake Storage Gen2 gebruiken met Azure HDInsight-clusters

Azure Data Lake Storage Gen2 is een cloudopslagservice die is toegelegd op big data-analyses, gebouwd op Azure Blob-opslag. Data Lake Storage Gen2 combineert de mogelijkheden van Azure Blob-opslag en Azure Data Lake Storage Gen1. De resulterende service biedt functies van Azure Data Lake Storage Gen1, zoals semantiek van bestandssystemen, beveiliging op mapniveau en bestandsniveau, samen met de goedkope, gelaagde opslag, hoge beschikbaarheid en mogelijkheden voor noodherstel azure blob-opslag.

## <a name="data-lake-storage-gen2-availability"></a>Beschikbaarheid data lake storage Gen2

Data Lake Storage Gen2 is beschikbaar als opslagoptie voor bijna alle Azure HDInsight-clustertypen als standaard- en extra opslagaccount. HBase kan echter slechts één Data Lake Storage Gen2-account hebben.

Zie [Opslagopties vergelijken voor gebruik met Azure HDInsight-clusters voor](hdinsight-hadoop-compare-storage-options.md)een volledige vergelijking van clustercreatieopties met Data Lake Storage Gen2.

> [!Note]  
> Nadat u Data Lake Storage Gen2 als **uw primaire opslagtype**hebt geselecteerd, u een Data Lake Storage Gen1-account niet selecteren als extra opslag.

## <a name="create-a-cluster-with-data-lake-storage-gen2-through-the-azure-portal"></a>Een cluster maken met Data Lake Storage Gen2 via de Azure-portal

Als u een HDInsight-cluster wilt maken dat Gegevens Lake Storage Gen2 voor opslag gebruikt, voert u de volgende stappen uit om een Data Lake Storage Gen2-account te configureren.

### <a name="create-a-user-assigned-managed-identity"></a>Een door de gebruiker toegewezen beheerde identiteit maken

Maak een door de gebruiker toegewezen beheerde identiteit als u er nog geen hebt.

1. Meld u aan bij [Azure Portal](https://portal.azure.com).
1. Klik linksboven op **Een resource maken**.
1. Typ gebruiker toegewezen **in** het zoekvak en klik op **Door gebruikers toegewezen beheerde identiteit**.
1. Klik **op Maken**.
1. Voer een naam in voor uw beheerde identiteit en selecteer het juiste abonnement, de resourcegroep en de locatie.
1. Klik **op Maken**.

Zie [Beheerde identiteiten in Azure HDInsight](hdinsight-managed-identities.md)voor meer informatie over hoe beheerde identiteiten werken in Azure HDInsight.

![Een door de gebruiker toegewezen beheerde identiteit maken](./media/hdinsight-hadoop-use-data-lake-storage-gen2/create-user-assigned-managed-identity-portal.png)

### <a name="create-a-data-lake-storage-gen2-account"></a>Een Data Lake Storage Gen2-account maken

Een Azure Data Lake Storage Gen2-opslagaccount maken.

1. Meld u aan bij [Azure Portal](https://portal.azure.com).
1. Klik linksboven op **Een resource maken**.
1. Typ **opslag** in het zoekvak en klik op **Opslagaccount**.
1. Klik **op Maken**.
1. Ga als het gaat om het scherm **Opslagaccount maken:**
    1. Selecteer de juiste abonnements- en resourcegroep.
    1. Voer een naam in voor uw Data Lake Storage Gen2-account. Zie [Conventies voor Azure-bronnen benoemen voor](/azure/azure-resource-manager/management/resource-name-rules#microsoftstorage)meer informatie over conventies voor opslagaccountnaamgeving.
    1. Klik op het tabblad **Geavanceerd.**
    1. Klik **op Ingeschakeld** naast **hiërarchische naamruimte** onder **Gegevensmeeropslag Gen2**.
    1. Klik op **Controleren + maken**.
    1. Klik **op Maken**

Zie [Snelstart: Een Azure Data Lake Storage Gen2-opslagaccount maken](../storage/blobs/data-lake-storage-quickstart-create-account.md)voor meer informatie over andere opties tijdens het maken van een opslagaccount voor opslagaccount .

![Schermafbeelding van het maken van opslagaccount in de Azure-portal](./media/hdinsight-hadoop-use-data-lake-storage-gen2/azure-data-lake-storage-account-create-advanced.png)

### <a name="set-up-permissions-for-the-managed-identity-on-the-data-lake-storage-gen2-account"></a>Machtigingen instellen voor de beheerde identiteit op het Data Lake Storage Gen2-account

Wijs de beheerde identiteit toe aan de rol **Storage Blob Data Owner** aan het opslagaccount.

1. Ga in de [Azure-portal](https://portal.azure.com)naar uw opslagaccount.
1. Selecteer uw opslagaccount en selecteer **vervolgens Toegangsbeheer (IAM)** om de toegangscontrole-instellingen voor het account weer te geven. Selecteer het tabblad **Toewijzingen van rollen** om de lijst met roltoewijzingen weer te geven.

    ![Schermafbeelding van instellingen voor opslagtoegangsbeheer](./media/hdinsight-hadoop-use-data-lake-storage-gen2/portal-access-control.png)

1. Selecteer de knop **+ Roltoewijzing toevoegen** om een nieuwe rol toe te voegen.
1. Selecteer in het venster **Roltoewijzing toevoegen** de rol **Opslagblobgegevenseigenaar.** Selecteer vervolgens het abonnement met het beheerde identiteits- en opslagaccount. Zoek vervolgens naar de door de gebruiker toegewezen beheerde identiteit die u eerder hebt gemaakt. Selecteer ten slotte de beheerde identiteit en deze wordt weergegeven onder **Geselecteerde leden**.

    ![Schermafbeelding van het toewijzen van een RBAC-rol](./media/hdinsight-hadoop-use-data-lake-storage-gen2/add-rbac-role3-window.png)

1. Selecteer **Opslaan**. De door de gebruiker toegewezen identiteit die u hebt geselecteerd, wordt nu weergegeven onder de geselecteerde rol.
1. Nadat deze eerste installatie is voltooid, u een cluster maken via de portal. Het cluster moet zich in hetzelfde Azure-gebied bevinden als het opslagaccount. Selecteer op het tabblad **Opslag** van het menu clustercreatie de volgende opties:

    * Selecteer Azure Data **Lake Storage Gen2**voor **primaire opslagtype**.
    * Zoek en selecteer onder **het primaire opslagaccount**het nieuw gemaakte Data Lake Storage Gen2-opslagaccount.

    * Selecteer **onder Identiteit**de nieuw aan de gebruiker toegewezen beheerde identiteit.

        ![Opslaginstellingen voor het gebruik van Data Lake Storage Gen2 met Azure HDInsight](./media/hdinsight-hadoop-use-data-lake-storage-gen2/azure-portal-cluster-storage-gentwo.png)

    > [!NOTE]
    > * Als u een secundair Data Lake Storage Gen2-account wilt toevoegen, wijst u op het niveau van de opslagaccount eenvoudig de eerder gemaakte beheerde identiteit toe aan het nieuwe Data Lake Storage Gen2-opslagaccount dat u wilt toevoegen. Houd er rekening mee dat het toevoegen van een secundair Data Lake Storage Gen2-account via het blade 'Extra opslagaccounts' op HDInsight niet wordt ondersteund.
    > * U RA-GRS of RA-ZRS inschakelen op het Azure-opslagaccount dat HDInsight gebruikt. Het maken van een cluster tegen het secundaire eindpunt RA-GRS of RA-ZRS wordt echter niet ondersteund.


## <a name="create-a-cluster-with-data-lake-storage-gen2-through-the-azure-cli"></a>Een cluster maken met Data Lake Storage Gen2 via de Azure CLI

U [een voorbeeldsjabloonbestand downloaden](https://github.com/Azure-Samples/hdinsight-data-lake-storage-gen2-templates/blob/master/hdinsight-adls-gen2-template.json) en [een voorbeeldparametersbestand downloaden.](https://github.com/Azure-Samples/hdinsight-data-lake-storage-gen2-templates/blob/master/parameters.json) Voordat u de onderstaande sjabloon en het Azure CLI-codefragment gebruikt, vervangt u de volgende tijdelijke aanduidingen door de juiste waarden:

| Tijdelijke aanduiding | Beschrijving |
|---|---|
| `<SUBSCRIPTION_ID>` | De id van uw Azure-abonnement |
| `<RESOURCEGROUPNAME>` | De resourcegroep waar u het nieuwe cluster- en opslagaccount wilt maken. |
| `<MANAGEDIDENTITYNAME>` | De naam van de beheerde identiteit die machtigingen krijgt voor uw Azure Data Lake Storage Gen2-account. |
| `<STORAGEACCOUNTNAME>` | Het nieuwe Azure Data Lake Storage Gen2-account dat wordt gemaakt. |
| `<CLUSTERNAME>` | De naam van uw HDInsight-cluster. |
| `<PASSWORD>` | Het door u gekozen wachtwoord voor het aanmelden bij het cluster met SSH en het Ambari-dashboard. |

Het onderstaande codefragment doet de volgende eerste stappen:

1. Logt in op uw Azure-account.
1. Hiermee stelt u het actieve abonnement in waar de bewerkingen worden uitgevoerd.
1. Hiermee maakt u een nieuwe resourcegroep voor de nieuwe implementatieactiviteiten.
1. Hiermee maakt u een door de gebruiker toegewezen beheerde identiteit.
1. Hiermee voegt u een extensie toe aan de Azure CLI om functies voor Data Lake Storage Gen2 te gebruiken.
1. Hiermee maakt u een nieuw Data `--hierarchical-namespace true` Lake Storage Gen2-account met behulp van de vlag.

```azurecli
az login
az account set --subscription <SUBSCRIPTION_ID>

# Create resource group
az group create --name <RESOURCEGROUPNAME> --location eastus

# Create managed identity
az identity create -g <RESOURCEGROUPNAME> -n <MANAGEDIDENTITYNAME>

az extension add --name storage-preview

az storage account create --name <STORAGEACCOUNTNAME> \
    --resource-group <RESOURCEGROUPNAME> \
    --location eastus --sku Standard_LRS \
    --kind StorageV2 --hierarchical-namespace true
```

Meld je vervolgens aan bij het portaal. Voeg de nieuwe door de gebruiker toegewezen beheerde identiteit toe aan de rol **Opslagblob-gegevensbijdrage** op het opslagaccount, zoals beschreven in stap 3 onder [De Azure-portal gebruiken](hdinsight-hadoop-use-data-lake-storage-gen2.md).

Nadat u de rol hebt toegewezen voor de door de gebruiker toegewezen beheerde identiteit, implementeert u de sjabloon met behulp van het volgende codefragment.

```azurecli
az group deployment create --name HDInsightADLSGen2Deployment \
    --resource-group <RESOURCEGROUPNAME> \
    --template-file hdinsight-adls-gen2-template.json \
    --parameters parameters.json
```

## <a name="create-a-cluster-with-data-lake-storage-gen2-through-azure-powershell"></a>Een cluster maken met Data Lake Storage Gen2 via Azure PowerShell

PowerShell gebruiken om een HDInsight-cluster te maken met Azure Data Lake Storage Gen2 wordt momenteel niet ondersteund.

## <a name="access-control-for-data-lake-storage-gen2-in-hdinsight"></a>Toegangscontrole voor Data Lake Storage Gen2 in HDInsight

### <a name="what-kinds-of-permissions-does-data-lake-storage-gen2-support"></a>Welke soorten machtigingen ondersteunt Data Lake Storage Gen2?

Data Lake Storage Gen2 maakt gebruik van een toegangscontrolemodel dat zowel rbac) als POSIX-achtige toegangscontrolelijsten (ACL's) ondersteunt. Data Lake Storage Gen1 ondersteunt toegangscontrolelijsten alleen voor het beheren van de toegang tot gegevens.

RBAC gebruikt roltoewijzingen om sets machtigingen effectief toe te passen op gebruikers, groepen en serviceprincipals voor Azure-resources. Deze Azure-resources zijn doorgaans beperkt tot bronnen op het hoogste niveau (bijvoorbeeld Azure Storage-accounts). Voor Azure Storage, en ook Data Lake Storage Gen2, is dit mechanisme uitgebreid naar de bron van het bestandssysteem.

 Zie [RBAC (Azure role-based access control)](../storage/blobs/data-lake-storage-access-control.md#azure-role-based-access-control-rbac)voor meer informatie over bestandsmachtigingen met RBAC.

Zie [Lijsten met toegangsbeheer voor bestanden en mappen](../storage/blobs/data-lake-storage-access-control.md#access-control-lists-on-files-and-directories)voor meer informatie over bestandsmachtigingen met ACL's.

### <a name="how-do-i-control-access-to-my-data-in-data-lake-storage-gen2"></a>Hoe beheer ik de toegang tot mijn gegevens in Data Lake Storage Gen2?

De mogelijkheid van uw HDInsight-cluster om toegang te krijgen tot bestanden in Data Lake Storage Gen2 wordt beheerd via beheerde identiteiten. Een beheerde identiteit is een identiteit die is geregistreerd in Azure Active Directory (Azure AD) waarvan de referenties worden beheerd door Azure. Met beheerde identiteiten hoeft u geen serviceprincipals te registreren in Azure AD of referenties zoals certificaten te onderhouden.

Azure-services hebben twee typen beheerde identiteiten: systeemtoegewezen en door de gebruiker toegewezen. HDInsight gebruikt door de gebruiker toegewezen beheerde identiteiten om toegang te krijgen tot Data Lake Storage Gen2. Een door de gebruiker toegewezen beheerde identiteit wordt gemaakt als een zelfstandige Azure-resource. Via een productieproces maakt Azure een identiteit in de Azure AD-tenant, die wordt vertrouwd door het gebruikte abonnement. Nadat de identiteit is gemaakt, kan deze worden toegewezen aan een of meer Azure-service-exemplaren.

De levenscyclus van een door de gebruiker toegewezen identiteit wordt afzonderlijk beheerd van de levenscyclus van de Azure Service-exemplaren waaraan de identiteit is toegewezen. Zie [Hoe werken de beheerde identiteiten voor Azure-resources voor](../active-directory/managed-identities-azure-resources/overview.md#how-does-the-managed-identities-for-azure-resources-work)meer informatie over beheerde identiteiten?

### <a name="how-do-i-set-permissions-for-azure-ad-users-to-query-data-in-data-lake-storage-gen2-by-using-hive-or-other-services"></a>Hoe stel ik machtigingen in voor Azure AD-gebruikers om gegevens op te vragen in Data Lake Storage Gen2 met behulp van Hive of andere services?

Als u machtigingen wilt instellen voor gebruikers om querygegevens in te stellen, gebruikt u Azure AD-beveiligingsgroepen als de toegewezen hoofd in ACL's. Wijs geen bestandstoegangsmachtigingen toe aan individuele gebruikers of serviceprincipals. Wanneer u Azure AD-beveiligingsgroepen gebruikt om de stroom van machtigingen te beheren, u gebruikers of serviceprincipals toevoegen en verwijderen zonder ACL's opnieuw toe te passen op een hele directorystructuur. U hoeft alleen de gebruikers toe te voegen of te verwijderen uit de juiste Azure AD-beveiligingsgroep. ACL's worden niet overgenomen, dus voor het opnieuw toepassen van ACL's moet de ACL in elk bestand en submap worden bijgewerkt.

## <a name="access-files-from-the-cluster"></a>Bestanden openen vanuit het cluster

Er zijn verschillende manieren waarop u toegang hebt tot de bestanden in Data Lake Storage Gen2 vanuit een HDInsight-cluster.

* **De volledig gekwalificeerde naam gebruiken**. Met deze methode geeft u het volledige pad op naar het bestand dat u wilt openen.

    ```
    abfs://<containername>@<accountname>.dfs.core.windows.net/<file.path>/
    ```

* **De verkorte padnotatie gebruiken**. Met deze benadering vervangt u het pad naar de clusterroot door:

    ```
    abfs:///<file.path>/
    ```

* **Het relatieve pad gebruiken**. Met deze methode geeft u alleen het volledige relatieve pad op naar het bestand dat u wilt openen.

    ```
    /<file.path>/
    ```

### <a name="data-access-examples"></a>Voorbeelden van gegevenstoegang

Voorbeelden zijn gebaseerd op een [ssh-verbinding](./hdinsight-hadoop-linux-use-ssh-unix.md) met het hoofdknooppunt van het cluster. De voorbeelden maken gebruik van alle drie de URI-schema's. De `CONTAINERNAME` `STORAGEACCOUNT` relevante waarden vervangen en doorde desbetreffende waarden

#### <a name="a-few-hdfs-commands"></a>Een paar hdfs-opdrachten

1. Maak een bestand over lokale opslag.

    ```bash
    touch testFile.txt
    ```

1. Maak mappen over clusteropslag.

    ```bash
    hdfs dfs -mkdir abfs://CONTAINERNAME@STORAGEACCOUNT.dfs.core.windows.net/sampledata1/
    hdfs dfs -mkdir abfs:///sampledata2/
    hdfs dfs -mkdir /sampledata3/
    ```

1. Kopieer gegevens uit lokale opslag naar clusteropslag.

    ```bash
    hdfs dfs -copyFromLocal testFile.txt  abfs://CONTAINERNAME@STORAGEACCOUNT.dfs.core.windows.net/sampledata1/
    hdfs dfs -copyFromLocal testFile.txt  abfs:///sampledata2/
    hdfs dfs -copyFromLocal testFile.txt  /sampledata3/
    ```

1. Lijst inhoud van de map op clusteropslag.

    ```bash
    hdfs dfs -ls abfs://CONTAINERNAME@STORAGEACCOUNT.dfs.core.windows.net/sampledata1/
    hdfs dfs -ls abfs:///sampledata2/
    hdfs dfs -ls /sampledata3/
    ```

#### <a name="creating-a-hive-table"></a>Een Hive-tabel maken

Drie bestandslocaties worden weergegeven voor illustratieve doeleinden. Gebruik voor de daadwerkelijke uitvoering `LOCATION` slechts één van de vermeldingen.

```hql
DROP TABLE myTable;
CREATE EXTERNAL TABLE myTable (
    t1 string,
    t2 string,
    t3 string,
    t4 string,
    t5 string,
    t6 string,
    t7 string)
ROW FORMAT DELIMITED FIELDS TERMINATED BY ' '
STORED AS TEXTFILE
LOCATION 'abfs://CONTAINERNAME@STORAGEACCOUNT.dfs.core.windows.net/example/data/';
LOCATION 'abfs:///example/data/';
LOCATION '/example/data/';
```

## <a name="next-steps"></a>Volgende stappen

* [Azure HDInsight-integratie met Data Lake Storage Gen2-preview - ACL en beveiligingsupdate](https://azure.microsoft.com/blog/azure-hdinsight-integration-with-data-lake-storage-gen-2-preview-acl-and-security-update/)
* [Inleiding tot Azure Data Lake Storage Gen2](../storage/blobs/data-lake-storage-introduction.md)
* [Zelfstudie: Gegevens extraheren, transformeren en laden met interactieve query in Azure HDInsight](./interactive-query/interactive-query-tutorial-analyze-flight-data.md)
