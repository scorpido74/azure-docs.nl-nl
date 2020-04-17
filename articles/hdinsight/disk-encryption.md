---
title: Door de klant beheerde sleutelschijfversleuteling voor Azure HDInsight
description: In dit artikel wordt beschreven hoe u uw eigen versleutelingssleutel uit Azure Key Vault gebruiken om gegevens te versleutelen die zijn opgeslagen op beheerde schijven in Azure HDInsight-clusters.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: hrasheed
ms.service: hdinsight
ms.topic: conceptual
ms.date: 04/15/2020
ms.openlocfilehash: 732709dbcb5ebe54025a963379128f1a1e74183e
ms.sourcegitcommit: 31ef5e4d21aa889756fa72b857ca173db727f2c3
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/16/2020
ms.locfileid: "81536298"
---
# <a name="customer-managed-key-disk-encryption"></a>Schijfversleuteling met behulp van door klant beheerde sleutel

Azure HDInsight ondersteunt door de klant beheerde sleutelversleuteling voor gegevens op beheerde schijven en resourceschijven die zijn gekoppeld aan virtuele machines van hdInsight-cluster. Met deze functie u Azure Key Vault gebruiken om de versleutelingssleutels te beheren die gegevens in rust op uw HDInsight-clusters beveiligen.

Alle beheerde schijven in HDInsight zijn beveiligd met Azure Storage Service Encryption (SSE). Standaard worden de gegevens op die schijven versleuteld met door Microsoft beheerde sleutels. Als u door de klant beheerde sleutels voor HDInsight inschakelt, verstrekt u de coderingssleutels voor HDInsight om deze sleutels te gebruiken en te beheren met Azure Key Vault.

Dit document heeft geen adres van gegevens die zijn opgeslagen in uw Azure Storage-account. Zie [Azure Storage-versleuteling voor gegevens in rust](../storage/common/storage-service-encryption.md)voor meer informatie over Azure Storage-versleuteling. Uw clusters kunnen een of meer gekoppelde Azure Storage-accounts hebben waarbij de versleutelingssleutels ook door Microsoft kunnen worden beheerd of door de klant worden beheerd, maar de versleutelingsservice is anders.

## <a name="introduction"></a>Inleiding

Door de klant beheerde sleutelversleuteling is een proces in één stap dat wordt afgehandeld tijdens het maken van een cluster zonder extra kosten. Het enige wat u hoeft te doen is HDInsight registreren als een beheerde identiteit met Azure Key Vault en de versleutelingssleutel toevoegen wanneer u uw cluster maakt.

Zowel resourceschijf als beheerde schijven op elk knooppunt van het cluster worden versleuteld met een symmetrische gegevensversleutelingssleutel (DEK). De DEK is beveiligd met behulp van de Key Encryption Key (KEK) uit uw sleutelkluis. De versleutelings- en decryptieprocessen worden volledig afgehandeld door Azure HDInsight.

Als de firewall voor de sleutelkluis is ingeschakeld op de sleutelkluis waar de schijfversleutelingssleutel is opgeslagen, moeten de IP-adressen van de REGIONALE Resourceprovider VAN HDInsight voor het gebied waar het cluster wordt geïmplementeerd, worden toegevoegd aan de configuratie van de sleutelkluisfirewall. Dit is nodig omdat HDInsight geen vertrouwde Azure-sleutelkluisservice is.

U de Azure-portal of Azure CLI gebruiken om de sleutels in de sleutelkluis veilig te draaien. Wanneer een sleutel roteert, begint het HDInsight-cluster de nieuwe sleutel binnen enkele minuten te gebruiken. Schakel de soft [delete-functies](../key-vault/general/overview-soft-delete.md) voor de beveiliging in om te beschermen tegen ransomware-scenario's en per ongeluk verwijderen. Sleutelkluizen zonder deze beveiligingsfunctie worden niet ondersteund.

|Clustertype |OS-schijf (beheerde schijf) |Gegevensschijf (beheerde schijf) |Tijdelijke gegevensschijf (lokale SSD) |
|---|---|---|---|
|Kafka, HBase met Accelerated schrijft|[SSE-versleuteling](https://docs.microsoft.com/azure/virtual-machines/windows/managed-disks-overview#encryption)|SSE-versleuteling + optionele CMK-versleuteling|Optionele CMK-versleuteling|
|Alle andere clusters (Spark, Interactive, Hadoop, HBase zonder Accelerated schrijft)|SSE-versleuteling|N.v.t.|Optionele CMK-versleuteling|

## <a name="get-started-with-customer-managed-keys"></a>Aan de slag met door de klant beheerde sleutels

Als u een HDInsight-cluster met klantbeheer ingeschakeld wilt maken, doorlopen we de volgende stappen:

1. Beheerde identiteiten maken voor Azure-bronnen
1. Azure Key Vault maken
1. Toets maken
1. Toegangsbeleid maken
1. HDInsight-cluster maken met klantbeheerde sleutel ingeschakeld
1. De versleutelingssleutel roteren

## <a name="create-managed-identities-for-azure-resources"></a>Beheerde identiteiten maken voor Azure-bronnen

Maak een door de gebruiker toegewezen beheerde identiteit om te verifiëren naar Key Vault.

Zie [Een door de gebruiker toegewezen beheerde identiteit maken](../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-portal.md) voor specifieke stappen. Zie [Beheerde identiteiten in Azure HDInsight](hdinsight-managed-identities.md)voor meer informatie over hoe beheerde identiteiten werken in Azure HDInsight. Zorg ervoor dat u de beheerde identiteitsbron-id opslaat voor wanneer u deze toevoegt aan het toegangsbeleid voor sleutelkluizen.

## <a name="create-azure-key-vault"></a>Azure Key Vault maken

Een sleutelkluis maken. Zie [Azure Key Vault maken](../key-vault/secrets/quick-create-portal.md) voor specifieke stappen.

HDInsight ondersteunt alleen Azure Key Vault. Als u uw eigen sleutelkluis hebt, u uw sleutels importeren in Azure Key Vault. Houd er rekening mee dat de sleutelkluis **Soft delete** moet hebben ingeschakeld. Ga voor meer informatie over het importeren van bestaande sleutels naar [Over sleutels, geheimen en certificaten](../key-vault/about-keys-secrets-and-certificates.md).

## <a name="create-key"></a>Toets maken

1. Navigeer vanuit uw nieuwe sleutelkluis naar > **Instellingentoetsen** > **+ Genereren/importeren**. **Settings**

    ![Een nieuwe sleutel genereren in Azure Key Vault](./media/disk-encryption/create-new-key.png "Een nieuwe sleutel genereren in Azure Key Vault")

1. Geef een naam op en selecteer **Maken**. Het **standaardsleuteltype** **RSA behouden**.

    ![genereert sleutelnaam](./media/disk-encryption/create-key.png "Sleutelnaam genereren")

1. Wanneer u terugkeert naar de pagina **Sleutels,** selecteert u de sleutel die u hebt gemaakt.

    ![key vault key list](./media/disk-encryption/key-vault-key-list.png)

1. Selecteer de versie om de pagina **Sleutelversie te** openen. Wanneer u uw eigen sleutel gebruikt voor HDInsight-clusterversleuteling, moet u de sleutel URI leveren. Kopieer de **sleutel-id** en sla deze ergens op totdat u klaar bent om uw cluster te maken.

    ![sleutel-id krijgen](./media/disk-encryption/get-key-identifier.png)

## <a name="create-access-policy"></a>Toegangsbeleid maken

1. Navigeer vanuit uw nieuwe sleutelkluis naar > **Instellingentoegangsbeleid** > **+ Toegangsbeleid toevoegen**. **Settings**

    ![Nieuw Azure Key Vault-toegangsbeleid maken](./media/disk-encryption/key-vault-access-policy.png)

1. Geef op de pagina **Toegangsbeleid toevoegen** de volgende gegevens op:

    |Eigenschap |Beschrijving|
    |---|---|
    |Belangrijkste machtigingen|Selecteer **Get,** **Uitpakken sleutel**en **Wrap Key**.|
    |Geheime machtigingen|Selecteer **Oppakken,** **instellen**en **verwijderen**.|
    |Hoofdprincipal selecteren|Selecteer de door de gebruiker toegewezen beheerde identiteit die u eerder hebt gemaakt.|

    ![Hoofdpunt selecteren instellen voor Azure Key Vault-toegangsbeleid](./media/disk-encryption/azure-portal-add-access-policy.png)

1. Selecteer **Toevoegen**.

1. Selecteer **Opslaan**.

    ![Azure Key Vault-toegangsbeleid opslaan](./media/disk-encryption/add-key-vault-access-policy-save.png)

## <a name="create-cluster-with-customer-managed-key-disk-encryption"></a>Cluster maken met door de klant beheerde sleutelschijfversleuteling

U bent nu klaar om een nieuw HDInsight-cluster te maken. Door de klant beheerde sleutel kan alleen worden toegepast op nieuwe clusters tijdens het maken van clusters. Versleuteling kan niet worden verwijderd uit door de klant beheerde sleutelclusters en de door de klant beheerde sleutel kan niet worden toegevoegd aan bestaande clusters.

### <a name="using-the-azure-portal"></a>Azure Portal gebruiken

Geef tijdens het maken van het cluster de volledige **sleutel-id**op, inclusief de belangrijkste versie. Bijvoorbeeld `https://contoso-kv.vault.azure.net/keys/myClusterKey/46ab702136bc4b229f8b10e8c2997fa4`. U moet ook de beheerde identiteit aan het cluster toewijzen en de belangrijkste URI opgeven.

![Nieuw cluster maken](./media/disk-encryption/create-cluster-portal.png)

### <a name="using-azure-cli"></a>Azure CLI gebruiken

In het volgende voorbeeld ziet u hoe u Azure CLI gebruikt om een nieuw Apache Spark-cluster te maken met schijfversleuteling ingeschakeld. Zie [Azure CLI az hdinsight create voor](https://docs.microsoft.com/cli/azure/hdinsight?view=azure-cli-latest#az-hdinsight-create)meer informatie.

```azurecli
az hdinsight create -t spark -g MyResourceGroup -n MyCluster \
-p "HttpPassword1234!" --workernode-data-disks-per-node 2 \
--storage-account MyStorageAccount \
--encryption-key-name SparkClusterKey \
--encryption-key-version 00000000000000000000000000000000 \
--encryption-vault-uri https://MyKeyVault.vault.azure.net \
--assign-identity MyMSI
```

### <a name="using-azure-resource-manager-templates"></a>Azure Resource Manager-sjablonen gebruiken

In het volgende voorbeeld ziet u hoe u een Azure Resource Manager-sjabloon gebruikt om een nieuw Apache Spark-cluster te maken waarmee schijfversleuteling is ingeschakeld. Zie [Wat zijn ARM-sjablonen voor](https://docs.microsoft.com/azure/azure-resource-manager/templates/overview)meer informatie?

In dit voorbeeld wordt PowerShell gebruikt om de sjabloon aan te roepen.

```powershell
$templateFile = "azuredeploy.json"
$ResourceGroupName = "MyResourceGroup"
$clusterName = "MyCluster"
$password = ConvertTo-SecureString 'HttpPassword1234!' -AsPlainText -Force
$diskEncryptionVaultUri = "https://MyKeyVault.vault.azure.net"
$diskEncryptionKeyName = "SparkClusterKey"
$diskEncryptionKeyVersion = "00000000000000000000000000000000"
$managedIdentityName = "MyMSI"

New-AzResourceGroupDeployment `
  -Name mySpark `
  -TemplateFile $templateFile `
  -ResourceGroupName $ResourceGroupName `
  -clusterName $clusterName `
  -clusterLoginPassword $password `
` -sshPassword $password `
  -diskEncryptionVaultUri $diskEncryptionVaultUri `
  -diskEncryptionKeyName $diskEncryptionKeyName `
  -diskEncryptionKeyVersion $diskEncryptionKeyVersion `
  -managedIdentityName $managedIdentityName
```

De inhoud van de `azuredeploy.json`sjabloon resourcebeheer:

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "0.9.0.0",
  "parameters": {
    "clusterName": {
      "type": "string",
      "metadata": {
        "description": "The name of the HDInsight cluster to create."
      }
    },
    "clusterLoginUserName": {
      "type": "string",
      "defaultValue": "admin",
      "metadata": {
        "description": "These credentials can be used to submit jobs to the cluster and to log into cluster dashboards."
      }
    },
    "clusterLoginPassword": {
      "type": "securestring",
      "metadata": {
        "description": "The password must be at least 10 characters in length and must contain at least one digit, one non-alphanumeric character, and one upper or lower case letter."
      }
    },
    "location": {
      "type": "string",
      "defaultValue": "[resourceGroup().location]",
      "metadata": {
        "description": "The location where all azure resources will be deployed."
      }
    },
    "sshUserName": {
      "type": "string",
      "defaultValue": "sshuser",
      "metadata": {
        "description": "These credentials can be used to remotely access the cluster."
      }
    },
    "sshPassword": {
      "type": "securestring",
      "metadata": {
        "description": "The password must be at least 10 characters in length and must contain at least one digit, one non-alphanumeric character, and one upper or lower case letter."
      }
    },
    "headNodeSize": {
      "type": "string",
      "defaultValue": "Standard_D12_v2",
      "metadata": {
        "description": "The VM size of the head nodes."
      }
    },
    "workerNodeSize": {
      "type": "string",
      "defaultValue": "Standard_D13_v2",
      "metadata": {
        "description": "The VM size of the worker nodes."
      }
    },
    "diskEncryptionVaultUri": {
      "type": "string",
      "metadata": {
        "description": "The Key Vault DNSname."
      }
    },
    "diskEncryptionKeyName": {
      "type": "string",
      "metadata": {
        "description": "The Key Vault key name."
      }
    },
    "diskEncryptionKeyVersion": {
      "type": "string",
      "metadata": {
        "description": "The Key Vault key version for the selected key."
      }
    },
    "managedIdentityName": {
      "type": "string",
      "metadata": {
        "description": "The user-assigned managed identity."
      }
    }
  },
  "variables": {
    "defaultStorageAccount": {
      "name": "[uniqueString(resourceGroup().id)]",
      "type": "Standard_LRS"
    }
  },
  "resources": [
    {
      "type": "Microsoft.Storage/storageAccounts",
      "name": "[variables('defaultStorageAccount').name]",
      "location": "[parameters('location')]",
      "apiVersion": "2019-06-01",
      "sku": {
        "name": "[variables('defaultStorageAccount').type]"
      },
      "kind": "Storage",
      "properties": {}
    },
    {
      "apiVersion": "2018-06-01-preview",
      "name": "[parameters('clusterName')]",
      "type": "Microsoft.HDInsight/clusters",
      "location": "[parameters('location')]",
      "properties": {
        "clusterVersion": "3.6",
        "osType": "Linux",
        "tier": "standard",
        "clusterDefinition": {
          "kind": "spark",
          "componentVersion": {
            "Spark": "2.3"
          },
          "configurations": {
            "gateway": {
              "restAuthCredential.isEnabled": true,
              "restAuthCredential.username": "[parameters('clusterLoginUserName')]",
              "restAuthCredential.password": "[parameters('clusterLoginPassword')]"
            }
          }
        },
        "storageProfile": {
          "storageaccounts": [
            {
              "name": "[replace(replace(reference(resourceId('Microsoft.Storage/storageAccounts', variables('defaultStorageAccount').name), '2019-06-01').primaryEndpoints.blob,'https://',''),'/','')]",
              "isDefault": true,
              "container": "[parameters('clusterName')]",
              "key": "[listKeys(resourceId('Microsoft.Storage/storageAccounts', variables('defaultStorageAccount').name), '2019-06-01').keys[0].value]"
            }
          ]
        },
        "computeProfile": {
          "roles": [
            {
              "name": "headnode",
              "minInstanceCount": 1,
              "targetInstanceCount": 2,
              "hardwareProfile": {
                "vmSize": "[parameters('headNodeSize')]"
              },
              "osProfile": {
                "linuxOperatingSystemProfile": {
                  "username": "[parameters('sshUserName')]",
                  "password": "[parameters('sshPassword')]"
                },
              },
            },
            {
              "name": "workernode",
              "targetInstanceCount": 1,
              "hardwareProfile": {
                "vmSize": "[parameters('workerNodeSize')]"
              },
              "osProfile": {
                "linuxOperatingSystemProfile": {
                  "username": "[parameters('sshUserName')]",
                  "password": "[parameters('sshPassword')]"
                },
              },
            }
          ]
        },
        "minSupportedTlsVersion": "1.2",
        "diskEncryptionProperties": {
          "vaultUri": "[parameters('diskEncryptionVaultUri')]",
          "keyName": "[parameters('diskEncryptionKeyName')]",
          "keyVersion": "[parameters('diskEncryptionKeyVersion')]",
          "msiResourceId": "[resourceID('Microsoft.ManagedIdentity/userAssignedIdentities/', parameters('managedIdentityName'))]"
        }
      },
      "identity": {
        "type": "UserAssigned",
        "userAssignedIdentities": {
          "[resourceID('Microsoft.ManagedIdentity/userAssignedIdentities/', parameters('managedIdentityName'))]": {}
        }
      }
    }
  ]
}
```

## <a name="rotating-the-encryption-key"></a>De versleutelingssleutel roteren

Er zijn mogelijk scenario's waarin u de versleutelingssleutels wilt wijzigen die door het HDInsight-cluster worden gebruikt nadat deze is gemaakt. Dit kan eenvoudig via het portaal. Voor deze bewerking moet het cluster toegang hebben tot zowel de huidige sleutel als de beoogde nieuwe sleutel, anders mislukt de bewerking van de roterende sleutel.

### <a name="using-the-azure-portal"></a>Azure Portal gebruiken

Om de sleutel te draaien, heb je de basissleutel kluis URI nodig. Zodra u dat hebt gedaan, gaat u naar de sectie HDInsight-clustereigenschappen in de portal en klikt u op **Sleutel wijzigen** onder URL van **de schijfversleutelingssleutel**. Voer de nieuwe sleutel-url in en verzend om de sleutel te roteren.

![schijfversleutelingssleutel roteren](./media/disk-encryption/change-key.png)

### <a name="using-azure-cli"></a>Azure CLI gebruiken

In het volgende voorbeeld ziet u hoe u de schijfversleutelingssleutel voor een bestaand HDInsight-cluster roteren. Zie [Azure CLI az hdinsight rotate-disk-encryption-key](https://docs.microsoft.com/cli/azure/hdinsight?view=azure-cli-latest#az-hdinsight-rotate-disk-encryption-key)voor meer informatie.

```azurecli
az hdinsight rotate-disk-encryption-key \
--encryption-key-name SparkClusterKey \
--encryption-key-version 00000000000000000000000000000000 \
--encryption-vault-uri https://MyKeyVault.vault.azure.net \
--name MyCluster \
--resource-group MyResourceGroup
```

## <a name="faq-for-customer-managed-key-encryption"></a>Veelgestelde vragen over door de klant beheerde sleutelversleuteling

**Hoe heeft het HDInsight-cluster toegang tot mijn sleutelkluis?**

HDInsight heeft toegang tot uw Azure Key Vault-exemplaar met behulp van de beheerde identiteit die u koppelt aan het HDInsight-cluster. Deze beheerde identiteit kan worden gemaakt voor of tijdens het maken van het cluster. U moet ook de beheerde identiteit toegang verlenen tot de sleutelkluis waar de sleutel is opgeslagen.

**Is deze functie beschikbaar voor alle clusters op HDInsight?**

Versleuteling van door de klant beheerde sleutelversleuteling is beschikbaar voor alle clustertypen, behalve Spark 2.1 en 2.2.

**Kan ik meerdere sleutels gebruiken om verschillende schijven of mappen te versleutelen?**

Nee, alle beheerde schijven en resourceschijven worden versleuteld met dezelfde sleutel.

**Wat gebeurt er als het cluster geen toegang meer heeft tot de sleutelkluis of de sleutel?**

Als het cluster de toegang tot de sleutel verliest, worden waarschuwingen weergegeven in de Apache Ambari-portal. In deze status mislukt de bewerking **Sleutel wijzigen.** Zodra de toegang tot de sleutel is hersteld, verdwijnen ambari-waarschuwingen en kunnen bewerkingen zoals sleutelrotatie met succes worden uitgevoerd.

![Belangrijke toegang Ambari alert](./media/disk-encryption/ambari-alert.png)

**Hoe kan ik het cluster herstellen als de sleutels worden verwijderd?**

Aangezien alleen de sleutels met "Soft Delete" worden ondersteund, moet het cluster, als de sleutels in de sleutelkluis worden hersteld, weer toegang krijgen tot de sleutels. Zie [AzKeyVaultKeyRemoval ongedaan maken](/powershell/module/az.keyvault/Undo-AzKeyVaultKeyRemoval) of [az-keyvault-key-recover.](/cli/azure/keyvault/key?view=azure-cli-latest#az-keyvault-key-recover)

**Welke schijftypen zijn versleuteld? Zijn os-schijven/resourceschijven ook versleuteld?**

Bronschijven en gegevens/beheerde schijven worden versleuteld. OS-schijven zijn niet versleuteld.

**Als een cluster wordt opgeschaald, ondersteunen de nieuwe knooppunten de door de klant beheerde sleutels dan naadloos?**

Ja. Het cluster heeft toegang nodig tot de sleutel in de sleutelkluis tijdens het opschalen. Dezelfde sleutel wordt gebruikt om zowel beheerde schijven als resourceschijven in het cluster te versleutelen.

**Zijn door de klant beheerde sleutels beschikbaar op mijn locatie?**

HDInsight door de klant beheerde sleutels zijn beschikbaar in alle openbare clouds en nationale clouds.

## <a name="next-steps"></a>Volgende stappen

* Zie Wat is Azure Key Vault voor meer informatie over Azure Key [Vault.](../key-vault/general/overview.md)
* [Overzicht van bedrijfsbeveiliging in Azure HDInsight](./domain-joined/hdinsight-security-overview.md).
