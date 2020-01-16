---
title: Door de klant beheerde sleutel versleuteling van de schijf voor Azure HDInsight
description: In dit artikel wordt beschreven hoe u uw eigen versleutelings sleutel gebruikt van Azure Key Vault voor het versleutelen van gegevens die zijn opgeslagen op Managed disks in azure HDInsight-clusters.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: hrasheed
ms.service: hdinsight
ms.topic: conceptual
ms.date: 01/06/2019
ms.openlocfilehash: 2c015db828bcbfa8b26f519b3a4707b5ec69b8f3
ms.sourcegitcommit: 3dc1a23a7570552f0d1cc2ffdfb915ea871e257c
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/15/2020
ms.locfileid: "75982494"
---
# <a name="customer-managed-key-disk-encryption"></a>Door de klant beheerde sleutel schijf versleuteling

Azure HDInsight ondersteunt door de klant beheerde sleutels, ook wel bekend als Bring Your Own Key (BYOK) versleuteling voor gegevens op beheerde schijven en bron schijven die zijn gekoppeld aan virtuele HDInsight-cluster-Vm's. Met deze functie kunt u Azure Key Vault gebruiken om de versleutelings sleutels te beheren waarmee gegevens in rust op uw HDInsight-clusters worden beveiligd. Uw clusters kunnen een of meer gekoppelde Azure Storage-accounts bevatten waarbij de versleutelings sleutels ook door micro soft worden beheerd of door de klant worden beheerd, maar de versleutelings service verschilt.

Dit document bevat geen informatie over gegevens die zijn opgeslagen in uw Azure Storage-account. Zie [Azure Storage versleuteling voor Data-at-rest](../storage/common/storage-service-encryption.md)voor meer informatie over ASE.

Alle beheerde schijven in HDInsight worden beveiligd met Azure Storage-service versleuteling (SSE). Standaard worden de gegevens op deze schijven versleuteld met door micro soft beheerde sleutels. Als u door de klant beheerde sleutels voor HDInsight inschakelt, geeft u de versleutelings sleutels voor HDInsight op om deze sleutels te gebruiken en te beheren met behulp van Azure Key Vault.

Door de klant beheerde sleutel versleuteling is een proces dat uit één stap wordt verwerkt tijdens het maken van het cluster zonder extra kosten. Het enige wat u hoeft te doen, is HDInsight registreren als een beheerde identiteit met Azure Key Vault en de versleutelings sleutel toevoegen wanneer u het cluster maakt.

Zowel de bron schijf als de beheerde schijven op elk knoop punt van het cluster worden versleuteld met een symmetrische gegevens versleutelings sleutel (DEK). De DEK wordt beschermd met behulp van de sleutel versleutelings sleutel (KEK) van uw sleutel kluis. De processen voor versleuteling en ontsleuteling worden volledig verwerkt door Azure HDInsight.

U kunt de Azure Portal of Azure CLI gebruiken om de sleutels in de sleutel kluis veilig te draaien. Wanneer een sleutel draait, begint het HDInsight-cluster binnen enkele minuten met de nieuwe sleutel. Schakel de beveiligings functies ' zacht verwijderen ' in om te beschermen tegen Ransomware-scenario's en onbedoeld verwijderen. Sleutel kluizen zonder deze beveiligings functie worden niet ondersteund.

## <a name="get-started-with-customer-managed-keys"></a>Aan de slag met door de klant beheerde sleutels

Als u een HDInsight-cluster met door de klant beheerde sleutels wilt maken, gaan we verder met de volgende stappen:

1. Beheerde identiteiten maken voor Azure-resources
2. Azure Key Vault en sleutels instellen
3. HDInsight-cluster maken met door de klant beheerde sleutel ingeschakeld
4. De versleutelings sleutel draaien

## <a name="create-managed-identities-for-azure-resources"></a>Beheerde identiteiten maken voor Azure-resources

Als u zich wilt verifiëren bij Key Vault, maakt u een door de gebruiker toegewezen beheerde identiteit met behulp van de [Azure Portal](../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-portal.md), [Azure PowerShell](../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-powershell.md), [Azure Resource Manager](../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-arm.md)of [Azure cli](../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-cli.md). Zie [beheerde identiteiten in azure hdinsight](hdinsight-managed-identities.md)voor meer informatie over de werking van beheerde identiteiten in azure hdinsight. Zorg ervoor dat u de resource-ID van de beheerde ID opslaat wanneer u deze toevoegt aan het Key Vault toegangs beleid.

## <a name="set-up-the-key-vault-and-keys"></a>De Key Vault en sleutels instellen

HDInsight ondersteunt alleen Azure Key Vault. Als u uw eigen sleutel kluis hebt, kunt u uw sleutels importeren in Azure Key Vault. Houd er rekening mee dat de sleutels ' zacht verwijderen ' moeten zijn. De functie ' zacht verwijderen ' is beschikbaar via de REST, .NET/C#, Power shell en Azure cli-interfaces.

1. Als u een nieuwe sleutel kluis wilt maken, volgt u de [Azure Key Vault](../key-vault/key-vault-overview.md) Snelstartgids. Ga voor meer informatie over het importeren van bestaande sleutels naar [sleutels, geheimen en certificaten](../key-vault/about-keys-secrets-and-certificates.md).

1. Schakel ' zacht-delete ' in op de sleutel kluis met behulp van de opdracht [AZ-kluis update](/cli/azure/keyvault?view=azure-cli-latest#az-keyvault-update) cli.

    ```azurecli
    az keyvault update --name <Key Vault Name> --enable-soft-delete
    ```

1. Sleutels maken.

    a. Als u een nieuwe sleutel wilt maken, selecteert u **genereren/importeren** in het menu **sleutels** onder **instellingen**.

    ![Een nieuwe sleutel in Azure Key Vault genereren](./media/disk-encryption/create-new-key.png "Een nieuwe sleutel in Azure Key Vault genereren")

    b. Stel **Opties** in voor het **genereren** en geven van de sleutel een naam.

    ![genereert sleutel naam](./media/disk-encryption/create-key.png "Sleutel naam genereren")

    c. Selecteer de sleutel die u hebt gemaakt in de lijst met sleutels.

    ![sleutel kluis sleutel lijst](./media/disk-encryption/key-vault-key-list.png)

    d. Wanneer u uw eigen sleutel voor HDInsight-cluster versleuteling gebruikt, moet u de sleutel-URI opgeven. Kopieer de **sleutel-id** en sla deze ergens op totdat u klaar bent om uw cluster te maken.

    ![sleutel-id ophalen](./media/disk-encryption/get-key-identifier.png)

1. Beheerde identiteit toevoegen aan het toegangs beleid voor de sleutel kluis.

    a. Maak een nieuw Azure Key Vault toegangs beleid.

    ![Nieuw toegangs beleid voor Azure Key Vault maken](./media/disk-encryption/add-key-vault-access-policy.png)

    b. Kies onder **Principal selecteren**de door de gebruiker toegewezen beheerde identiteit die u hebt gemaakt.

    ![Selecteer de principal voor Azure Key Vault toegangs beleid instellen](./media/disk-encryption/add-key-vault-access-policy-select-principal.png)

    c. Stel de **sleutel machtigingen** in om de sleutel op te **halen**, uit te **pakken**en te **verpakken**.

    ![Sleutel machtigingen instellen voor Azure Key Vault toegang policy1](./media/disk-encryption/add-key-vault-access-policy-keys.png "Sleutel machtigingen instellen voor Azure Key Vault toegang policy1")

    d. Stel **geheime machtigingen** in op **Get**, **set**en **Delete**.

    ![Sleutel machtigingen instellen voor Azure Key Vault toegang policy2](./media/disk-encryption/add-key-vault-access-policy-secrets.png "Sleutel machtigingen instellen voor Azure Key Vault toegang policy2")

    e. Selecteer **Opslaan**.

    ![Azure Key Vault toegangs beleid opslaan](./media/disk-encryption/add-key-vault-access-policy-save.png)

## <a name="create-cluster-with-customer-managed-key-disk-encryption"></a>Een cluster maken met door de klant beheerde sleutel schijf versleuteling

U bent nu klaar om een nieuw HDInsight-cluster te maken. Door de klant beheerde sleutel kan alleen worden toegepast op nieuwe clusters tijdens het maken van het cluster. Versleuteling kan niet worden verwijderd uit door de klant beheerde sleutel clusters en de door de klant beheerde sleutel kan niet worden toegevoegd aan bestaande clusters.

### <a name="using-the-azure-portal"></a>Azure Portal gebruiken

Geef tijdens het maken van het cluster de volledige sleutel-URL op, met inbegrip van de sleutel versie. Bijvoorbeeld `https://contoso-kv.vault.azure.net/keys/myClusterKey/46ab702136bc4b229f8b10e8c2997fa4`. U moet ook de beheerde identiteit toewijzen aan het cluster en de sleutel-URI opgeven.

### <a name="using-azure-cli"></a>Azure CLI gebruiken

In het volgende voor beeld ziet u hoe u Azure CLI gebruikt om een nieuw Apache Spark-cluster te maken waarvoor schijf versleuteling is ingeschakeld. Zie de documentatie van [Azure cli AZ hdinsight maken](https://docs.microsoft.com/cli/azure/hdinsight?view=azure-cli-latest#az-hdinsight-create) voor meer informatie.

```azurecli
az hdinsight create -t spark -g MyResourceGroup -n MyCluster \
-p "HttpPassword1234!" --workernode-data-disks-per-node 2 \
--storage-account MyStorageAccount \
--encryption-key-name SparkClusterKey \
--encryption-key-version 00000000000000000000000000000000 \
--encryption-vault-uri https://MyKeyVault.vault.azure.net \
--assign-identity MyMSI
```

## <a name="rotating-the-encryption-key"></a>De versleutelings sleutel draaien

Er zijn mogelijk scenario's waarin u mogelijk de versleutelings sleutels wilt wijzigen die door het HDInsight-cluster worden gebruikt nadat het is gemaakt. Dit kan eenvoudig zijn via de portal. Voor deze bewerking moet het cluster toegang hebben tot zowel de huidige sleutel als de beoogde nieuwe sleutel, anders kan de bewerking voor het draaien van de sleutel niet worden uitgevoerd.

### <a name="using-the-azure-portal"></a>Azure Portal gebruiken

Als u de sleutel wilt draaien, moet u de volledige URL van de nieuwe sleutel hebben (zie stap 3 van [de Key Vault en sleutels instellen](#set-up-the-key-vault-and-keys)). Als u dat hebt gedaan, gaat u naar de sectie eigenschappen van HDInsight-cluster in de portal en klikt u op de **sleutel wijzigen** onder URL voor de **schijf versleutelings sleutel**. Voer in de nieuwe sleutel-URL in en verzend de sleutel om deze te draaien.

![schijf versleutelings sleutel draaien](./media/disk-encryption/change-key.png)

### <a name="using-azure-cli"></a>Azure CLI gebruiken

In het volgende voor beeld ziet u hoe u de schijf versleutelings sleutel voor een bestaand HDInsight-cluster roteert. Zie [Azure cli AZ hdinsight Rotate-Disk-Encryption-Key](https://docs.microsoft.com/cli/azure/hdinsight?view=azure-cli-latest#az-hdinsight-rotate-disk-encryption-key) voor meer informatie.

```azurecli
az hdinsight rotate-disk-encryption-key \
--encryption-key-name SparkClusterKey \
--encryption-key-version 00000000000000000000000000000000 \
--encryption-vault-uri https://MyKeyVault.vault.azure.net \
--name MyCluster \
--resource-group MyResourceGroup
```

## <a name="faq-for-customer-managed-key-encryption"></a>Veelgestelde vragen over versleuteling door de klant beheerde sleutel

**Hoe opent het HDInsight-cluster mijn sleutel kluis?**

HDInsight opent uw Azure Key Vault-exemplaar met behulp van de beheerde identiteit die u aan het HDInsight-cluster koppelt. Deze beheerde identiteit kan worden gemaakt vóór of tijdens het maken van het cluster. U moet ook de beheerde identiteits toegang verlenen aan de sleutel kluis waar de sleutel wordt opgeslagen.

**Is deze functie beschikbaar voor alle clusters in HDInsight?**

Door de klant beheerde sleutel versleuteling is beschikbaar voor alle cluster typen behalve Spark 2,1 en 2,2.

**Kan ik meerdere sleutels gebruiken voor het versleutelen van verschillende schijven of mappen?**

Nee, alle beheerde schijven en bron schijven worden versleuteld met dezelfde sleutel.

**Wat gebeurt er als het cluster geen toegang meer heeft tot de sleutel kluis of de sleutel?**

Als het cluster de toegang tot de sleutel verliest, worden waarschuwingen weer gegeven in de Apache Ambari-Portal. In deze status mislukt de **wijzigings sleutel** bewerking. Zodra de toegang tot de sleutel is hersteld, worden de Ambari-waarschuwingen verwijderd en worden bewerkingen, zoals het draaien van sleutels, kunnen worden uitgevoerd.

![Ambari-waarschuwing voor Key Access](./media/disk-encryption/ambari-alert.png)

**Hoe kan ik het cluster herstellen als de sleutels worden verwijderd?**

Omdat alleen de sleutels "voorlopig verwijderen" worden ondersteund en de sleutels worden hersteld in de sleutel kluis, moet het cluster weer toegang krijgen tot de sleutels. Als u een Azure Key Vault sleutel wilt herstellen, raadpleegt u [Undo-AzKeyVaultKeyRemoval](/powershell/module/az.keyvault/Undo-AzKeyVaultKeyRemoval) of [AZ-sleutel kluis-Key-Recover](/cli/azure/keyvault/key?view=azure-cli-latest#az-keyvault-key-recover).

**Welke schijf typen zijn versleuteld? Zijn besturingssysteem schijven/bron schijven ook versleuteld?**

Bron schijven en gegevens/beheerde schijven worden versleuteld. BESTURINGSSYSTEEM schijven zijn niet versleuteld.

**Als een cluster omhoog wordt geschaald, worden door de nieuwe knoop punten naadloos door de klant beheerde sleutels ondersteund?**

Ja. Het cluster moet toegang hebben tot de sleutel in de sleutel kluis tijdens het omhoog schalen. Dezelfde sleutel wordt gebruikt voor het versleutelen van zowel beheerde schijven als bron schijven in het cluster.

**Zijn de door de klant beheerde sleutels beschikbaar op mijn locatie?**

Door de klant beheerde sleutels van HDInsight zijn beschikbaar in alle open bare Clouds en nationale Clouds.

## <a name="next-steps"></a>Volgende stappen

* [Overzicht van ENTER prise Security in azure HDInsight](./domain-joined/hdinsight-security-overview.md)
