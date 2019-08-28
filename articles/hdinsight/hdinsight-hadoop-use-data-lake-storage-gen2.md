---
title: Azure Data Lake Storage Gen2 gebruiken met Azure HDInsight-clusters
description: Meer informatie over het gebruik van Azure Data Lake Storage Gen2 met Azure HDInsight-clusters.
author: hrasheed-msft
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 08/27/2019
ms.author: hrasheed
ms.openlocfilehash: e06d6473a47dcff3506843150375c70ed2bd8cea
ms.sourcegitcommit: 388c8f24434cc96c990f3819d2f38f46ee72c4d8
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 08/27/2019
ms.locfileid: "70061836"
---
# <a name="use-azure-data-lake-storage-gen2-with-azure-hdinsight-clusters"></a>Azure Data Lake Storage Gen2 gebruiken met Azure HDInsight-clusters

Azure Data Lake Storage Gen2 is een service voor Cloud opslag die is toegewezen aan big data Analytics, gebouwd op Azure Blob-opslag. Data Lake Storage Gen2 combineert de mogelijkheden van Azure Blob-opslag en Azure Data Lake Storage Gen1. De resulterende service biedt functies van Azure Data Lake Storage Gen1, zoals bestandssysteem semantiek, beveiliging op Directory-en bestands niveau en schaal baarheid, samen met de lage kosten, gelaagde opslag, hoge Beschik baarheid en mogelijkheden voor herstel na nood gevallen vanuit Azure Blob-opslag.

## <a name="data-lake-storage-gen2-availability"></a>Beschik baarheid Data Lake Storage Gen2

Data Lake Storage Gen2 is beschikbaar als een opslag optie voor bijna alle Azure HDInsight-cluster typen als standaard en als een extra opslag account. HBase kan echter slechts één Data Lake Storage Gen2 account hebben.

Zie [opslag opties vergelijken voor gebruik met Azure HDInsight-clusters](hdinsight-hadoop-compare-storage-options.md)voor een volledige vergelijking van de opties voor het maken van clusters met behulp van data Lake Storage Gen2.

> [!Note]  
> Nadat u Data Lake Storage Gen2 hebt geselecteerd als uw **primaire opslag type**, kunt u een Data Lake Storage gen1 account niet selecteren als extra opslag.

## <a name="create-a-cluster-with-data-lake-storage-gen2-through-the-azure-portal"></a>Een cluster met Data Lake Storage Gen2 maken via de Azure Portal

Als u een HDInsight-cluster wilt maken dat gebruikmaakt van Data Lake Storage Gen2 voor opslag, voert u de volgende stappen uit om een Data Lake Storage Gen2-account te configureren.

### <a name="create-a-user-assigned-managed-identity"></a>Een door de gebruiker toegewezen beheerde identiteit maken

Maak een door de gebruiker toegewezen beheerde identiteit als u deze nog niet hebt. Zie een [rol maken, weer geven, verwijderen of toewijzen aan een door de gebruiker toegewezen beheerde identiteit met behulp van de Azure Portal](../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-portal.md#create-a-user-assigned-managed-identity). Zie [beheerde identiteiten in azure hdinsight](hdinsight-managed-identities.md)voor meer informatie over de werking van beheerde identiteiten in azure hdinsight.

![Een door de gebruiker toegewezen beheerde identiteit maken](./media/hdinsight-hadoop-use-data-lake-storage-gen2/create-user-assigned-managed-identity-portal.png)

### <a name="create-a-data-lake-storage-gen2-account"></a>Een Data Lake Storage Gen2-account maken

Een Azure Data Lake Storage Gen2-opslagaccount maken. Zorg ervoor dat de optie **hiërarchische naam ruimte** is ingeschakeld. Zie voor meer informatie [Snelstart: Maak een Azure Data Lake Storage Gen2 Storage-](../storage/blobs/data-lake-storage-quickstart-create-account.md)account.

![Scherm opname van het maken van een opslag account in de Azure Portal](./media/hdinsight-hadoop-data-lake-storage-gen2/azure-data-lake-storage-account-create-advanced.png)

### <a name="set-up-permissions-for-the-managed-identity-on-the-data-lake-storage-gen2-account"></a>Machtigingen instellen voor de beheerde identiteit op het Data Lake Storage Gen2-account

Wijs de beheerde identiteit toe aan de rol van de eigenaar van de **opslag-BLOB** voor het opslag account. Zie voor meer informatie, [beheren toegangsrechten tot Azure BLOB Storage en Queue gegevens met RBAC (Preview)](../storage/common/storage-auth-aad-rbac.md).

1. Ga in het [Azure Portal](https://portal.azure.com)naar uw opslag account.
1. Selecteer uw opslag account en selecteer vervolgens **toegangs beheer (IAM)** om de instellingen voor toegangs beheer voor het account weer te geven. Selecteer het tabblad roltoewijzingen om de lijst met roltoewijzingen weer te geven.
    
    ![Scherm opname van instellingen voor opslag toegangs beheer](./media/hdinsight-hadoop-data-lake-storage-gen2/portal-access-control.png)
    
1. Selecteer de knop **+ roltoewijzing toevoegen** om een nieuwe rol toe te voegen.
1. Selecteer in het venster **roltoewijzing toevoegen** de rol **Storage BLOB-gegevens eigenaar** . Selecteer vervolgens het abonnement met het beheerde identiteits-en opslag account. Zoek vervolgens naar de door de gebruiker toegewezen beheerde identiteit die u eerder hebt gemaakt. Ten slotte selecteert u de beheerde identiteit en wordt deze weer gegeven onder **geselecteerde leden**.
    
    ![Scherm afbeelding die laat zien hoe u een RBAC-rol toewijst](./media/hdinsight-hadoop-data-lake-storage-gen2/add-rbac-role3.png)
    
1. Selecteer **Opslaan**. De gebruikers-id die u hebt geselecteerd, wordt nu weer gegeven onder de geselecteerde rol.
1. Nadat deze initiële installatie is voltooid, kunt u een cluster maken via de portal. Het cluster moet zich in dezelfde Azure-regio bevinden als het opslag account. Selecteer in de sectie **opslag** van het menu cluster maken de volgende opties:
        
    * Selecteer **Azure data Lake Storage Gen2**voor het **primaire opslag type**.
    * Onder **Selecteer een opslag account**zoekt en selecteert u het zojuist gemaakte data Lake Storage Gen2-opslag account.
        
        ![Opslag instellingen voor het gebruik van Data Lake Storage Gen2 met Azure HDInsight](./media/hdinsight-hadoop-data-lake-storage-gen2/primary-storage-type-adls-gen2.png)
    
    * Onder **identiteit**selecteert u het juiste abonnement en de zojuist gemaakte door de gebruiker toegewezen beheerde identiteit.
        
        ![Identiteits instellingen voor het gebruik van Data Lake Storage Gen2 met Azure HDInsight](./media/hdinsight-hadoop-data-lake-storage-gen2/managed-identity-cluster-creation.png)
        
> [!Note]
> Als u een secundaire Data Lake Storage Gen2 account wilt toevoegen, moet u op het niveau van het opslag account eenvoudigweg de beheerde identiteit toewijzen die u eerder hebt gemaakt voor het nieuwe Data Lake Storage Gen2 opslag account dat u wilt toevoegen. Het is niet toegestaan om een secundaire Data Lake Storage Gen2-account toe te voegen via de Blade ' extra opslag accounts ' op HDInsight. 

## <a name="create-a-cluster-with-data-lake-storage-gen2-through-the-azure-cli"></a>Een cluster met Data Lake Storage Gen2 maken via de Azure CLI

U kunt [een voorbeeld sjabloon bestand downloaden](https://github.com/Azure-Samples/hdinsight-data-lake-storage-gen2-templates/blob/master/hdinsight-adls-gen2-template.json) en [een voor beeld-parameter bestand downloaden](https://github.com/Azure-Samples/hdinsight-data-lake-storage-gen2-templates/blob/master/parameters.json). Voordat u de sjabloon en het Azure CLI-code fragment hieronder gebruikt, vervangt u de volgende tijdelijke aanduidingen door de juiste waarden:

| Tijdelijke aanduiding | Description |
|---|---|
| `<SUBSCRIPTION_ID>` | De ID van uw Azure-abonnement |
| `<RESOURCEGROUPNAME>` | De resource groep waar u het nieuwe cluster en opslag account wilt maken. |
| `<MANAGEDIDENTITYNAME>` | De naam van de beheerde identiteit die machtigingen krijgt voor uw Azure Data Lake Storage Gen2-account. |
| `<STORAGEACCOUNTNAME>` | Het nieuwe Azure Data Lake Storage Gen2-account dat wordt gemaakt. |
| `<CLUSTERNAME>` | De naam van uw HDInsight-cluster. |
| `<PASSWORD>` | Uw gekozen wacht woord voor het aanmelden bij het cluster met behulp van SSH en het Ambari-dash board. |

In het onderstaande code fragment worden de volgende eerste stappen uitgevoerd:

1. Meld u aan bij uw Azure-account.
1. Hiermee stelt u het actieve abonnement in waarin de bewerkingen worden gemaakt.
1. Hiermee maakt u een nieuwe resource groep voor de nieuwe implementatie activiteiten. 
1. Hiermee maakt u een door de gebruiker toegewezen beheerde identiteit.
1. Hiermee wordt een extensie toegevoegd aan de Azure CLI om functies voor Data Lake Storage Gen2 te gebruiken.
1. Hiermee maakt u een nieuw data Lake Storage Gen2-account `--hierarchical-namespace true` met behulp van de vlag. 

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

Meld u vervolgens aan bij de portal. Voeg de nieuwe door de gebruiker toegewezen beheerde identiteit toe aan de rol voor het overdragen van blobgegevens op het opslag account, zoals beschreven in stap 3 onder [het gebruik van de Azure Portal](hdinsight-hadoop-use-data-lake-storage-gen2.md).

Nadat u de rol hebt toegewezen aan de door de gebruiker toegewezen beheerde identiteit, implementeert u de sjabloon met behulp van het volgende code fragment.

```azurecli
az group deployment create --name HDInsightADLSGen2Deployment \
    --resource-group <RESOURCEGROUPNAME> \
    --template-file hdinsight-adls-gen2-template.json \
    --parameters parameters.json
```

## <a name="access-control-for-data-lake-storage-gen2-in-hdinsight"></a>Toegangs beheer voor Data Lake Storage Gen2 in HDInsight

### <a name="what-kinds-of-permissions-does-data-lake-storage-gen2-support"></a>Welke soorten machtigingen ondersteunt Data Lake Storage Gen2?

Data Lake Storage Gen2 maakt gebruik van een model voor toegangs beheer dat zowel op rollen gebaseerde toegangs beheer (RBAC) als POSIX-achtige Acl's (Access Control List) ondersteunt. Data Lake Storage Gen1 ondersteunt alleen toegangs beheer lijsten voor het beheren van de toegang tot gegevens.

RBAC gebruikt roltoewijzingen voor het effectief Toep assen van machtigingen sets voor gebruikers, groepen en service-principals voor Azure-resources. Normaal gesp roken zijn deze Azure-resources beperkt tot resources op het hoogste niveau (bijvoorbeeld Azure Storage-accounts). Voor Azure Storage en ook Data Lake Storage Gen2 is dit mechanisme uitgebreid naar de bestands systeem bron.

 Zie [Azure Role-based Access Control (RBAC)](../storage/blobs/data-lake-storage-access-control.md#azure-role-based-access-control-rbac)voor meer informatie over bestands machtigingen met RBAC.

Zie [toegangs beheer lijsten voor bestanden en mappen](../storage/blobs/data-lake-storage-access-control.md#access-control-lists-on-files-and-directories)voor meer informatie over bestands machtigingen met acl's.

### <a name="how-do-i-control-access-to-my-data-in-data-lake-storage-gen2"></a>Hoe kan ik de toegang tot mijn gegevens in Data Lake Storage Gen2 beheren?

De mogelijkheid van uw HDInsight-cluster voor toegang tot bestanden in Data Lake Storage Gen2 wordt beheerd via beheerde identiteiten. Een beheerde identiteit is een identiteit die is geregistreerd in Azure Active Directory (Azure AD) waarvan de referenties door Azure worden beheerd. Met beheerde identiteiten hoeft u geen service-principals te registreren in azure AD of referenties zoals certificaten te behouden.

Azure-Services hebben twee typen beheerde identiteiten: systeem toegewezen en door de gebruiker toegewezen. HDInsight maakt gebruik van door de gebruiker toegewezen beheerde identiteiten voor toegang tot Data Lake Storage Gen2. Een door de gebruiker toegewezen beheerde identiteit wordt gemaakt als een zelfstandige Azure-resource. Via een productieproces maakt Azure een identiteit in de Azure AD-tenant, die wordt vertrouwd door het gebruikte abonnement. Nadat de identiteit is gemaakt, kan deze worden toegewezen aan een of meer Azure-service-exemplaren.

De levenscyclus van een door de gebruiker toegewezen identiteit wordt afzonderlijk beheerd van de levenscyclus van de Azure Service-exemplaren waaraan de identiteit is toegewezen. Zie [Hoe worden de beheerde identiteiten voor Azure-resources gewerkt?](../active-directory/managed-identities-azure-resources/overview.md#how-does-the-managed-identities-for-azure-resources-work)voor meer informatie over beheerde identiteiten.

### <a name="how-do-i-set-permissions-for-azure-ad-users-to-query-data-in-data-lake-storage-gen2-by-using-hive-or-other-services"></a>Hoe kan ik machtigingen instellen voor Azure AD-gebruikers om gegevens op te vragen in Data Lake Storage Gen2 met behulp van Hive of andere services?

Als u machtigingen voor gebruikers wilt instellen om gegevens op te vragen, gebruikt u Azure AD-beveiligings groepen als de toegewezen Principal in Acl's. Wijs niet rechtstreeks machtigingen voor bestands toegang toe aan afzonderlijke gebruikers of service-principals. Wanneer u Azure AD-beveiligings groepen gebruikt om de stroom van machtigingen te beheren, kunt u gebruikers of service-principals toevoegen en verwijderen zonder dat Acl's opnieuw worden toegepast op een volledige mapstructuur. U hoeft alleen de gebruikers toe te voegen aan of te verwijderen uit de juiste Azure AD-beveiligings groep. Acl's worden niet overgenomen. Als u Acl's opnieuw wilt Toep assen, moet u de toegangs beheer lijst voor elk bestand en elke submap bijwerken.

## <a name="next-steps"></a>Volgende stappen

* [Integratie van Azure HDInsight met Data Lake Storage Gen2-voor beeld-ACL en beveiligings update](https://azure.microsoft.com/blog/azure-hdinsight-integration-with-data-lake-storage-gen-2-preview-acl-and-security-update/)
* [Inleiding tot Azure Data Lake Storage Gen2](../storage/blobs/data-lake-storage-introduction.md)
