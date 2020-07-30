---
title: Door de klant beheerde sleutel versleuteling van de schijf voor Azure HDInsight
description: In dit artikel wordt beschreven hoe u uw eigen versleutelings sleutel gebruikt van Azure Key Vault voor het versleutelen van gegevens die zijn opgeslagen op Managed disks in azure HDInsight-clusters.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: hrasheed
ms.service: hdinsight
ms.topic: conceptual
ms.date: 04/15/2020
ms.openlocfilehash: a8bb9dc5aa6ebbd4ef7fb1b9550670a3c6298333
ms.sourcegitcommit: 5b8fb60a5ded05c5b7281094d18cf8ae15cb1d55
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 07/29/2020
ms.locfileid: "87387843"
---
# <a name="customer-managed-key-disk-encryption"></a>Schijfversleuteling met behulp van door klant beheerde sleutel

Azure HDInsight ondersteunt door de klant beheerde sleutel versleuteling voor gegevens op beheerde schijven en bron schijven die zijn gekoppeld aan virtuele machines van het HDInsight-cluster. Met deze functie kunt u Azure Key Vault gebruiken om de versleutelings sleutels te beheren waarmee gegevens in rust op uw HDInsight-clusters worden beveiligd.

Alle beheerde schijven in HDInsight worden beveiligd met Azure Storage-service versleuteling (SSE). Standaard worden de gegevens op deze schijven versleuteld met door micro soft beheerde sleutels. Als u door de klant beheerde sleutels voor HDInsight inschakelt, geeft u de versleutelings sleutels voor HDInsight op om deze sleutels te gebruiken en te beheren met behulp van Azure Key Vault.

Dit document bevat geen informatie over de gegevens die zijn opgeslagen in uw Azure Storage-account. Zie [Azure Storage encryption for Data at rest](../storage/common/storage-service-encryption.md)(Engelstalig) voor meer informatie over Azure Storage versleuteling. Uw clusters kunnen een of meer gekoppelde Azure Storage-accounts bevatten waarbij de versleutelings sleutels ook door micro soft worden beheerd of door de klant worden beheerd, maar de versleutelings service verschilt.

## <a name="introduction"></a>Inleiding

Door de klant beheerde sleutel versleuteling is een proces dat uit één stap wordt verwerkt tijdens het maken van het cluster zonder extra kosten. Het enige wat u hoeft te doen, is HDInsight registreren als een beheerde identiteit met Azure Key Vault en de versleutelings sleutel toevoegen wanneer u het cluster maakt.

Zowel de bron schijf als de beheerde schijven op elk knoop punt van het cluster worden versleuteld met een symmetrische gegevens versleutelings sleutel (DEK). De DEK wordt beschermd met behulp van de sleutel versleutelings sleutel (KEK) van uw sleutel kluis. De processen voor versleuteling en ontsleuteling worden volledig verwerkt door Azure HDInsight.

Als de sleutel kluis firewall is ingeschakeld op de sleutel kluis waarop de schijf versleutelings sleutel is opgeslagen, moeten de IP-adressen van de HDInsight regionale resource provider voor de regio waar het cluster wordt geïmplementeerd, worden toegevoegd aan de firewall configuratie van de sleutel kluis. Dit is nodig omdat HDInsight geen vertrouwde Azure Key kluis-service is.

U kunt de Azure Portal of Azure CLI gebruiken om de sleutels in de sleutel kluis veilig te draaien. Wanneer een sleutel draait, begint het HDInsight-cluster binnen enkele minuten met de nieuwe sleutel. Schakel de beveiliging van de functie voor het [voorlopig verwijderen](../key-vault/general/soft-delete-overview.md) van sleutels in voor beveiliging tegen Ransomware-scenario's en onbedoeld verwijderen. Sleutel kluizen zonder deze beveiligings functie worden niet ondersteund.

|Cluster type |BESTURINGSSYSTEEM schijf (beheerde schijf) |Gegevens schijf (beheerde schijf) |Tijdelijke gegevens schijf (lokale SSD) |
|---|---|---|---|
|Kafka, HBase met versnelde schrijf bewerkingen|[SSE-versleuteling](https://docs.microsoft.com/azure/virtual-machines/windows/managed-disks-overview#encryption)|SSE-versleuteling + optionele CMK-versleuteling|Optionele CMK-versleuteling|
|Alle andere clusters (Spark, Interactive, Hadoop, HBase zonder versnelde schrijf bewerkingen)|SSE-versleuteling|N.v.t.|Optionele CMK-versleuteling|

## <a name="get-started-with-customer-managed-keys"></a>Aan de slag met door de klant beheerde sleutels

Als u een HDInsight-cluster met door de klant beheerde sleutels wilt maken, gaan we verder met de volgende stappen:

1. Beheerde identiteiten maken voor Azure-resources
1. Azure Key Vault maken
1. Sleutel maken
1. Toegangs beleid maken
1. HDInsight-cluster maken met door de klant beheerde sleutel ingeschakeld
1. De versleutelings sleutel draaien

## <a name="create-managed-identities-for-azure-resources"></a>Beheerde identiteiten maken voor Azure-resources

Maak een door de gebruiker toegewezen beheerde identiteit om te verifiëren bij Key Vault.

Zie [een door de gebruiker toegewezen beheerde identiteit maken](../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-portal.md) voor specifieke stappen. Zie [beheerde identiteiten in azure hdinsight](hdinsight-managed-identities.md)voor meer informatie over de werking van beheerde identiteiten in azure hdinsight. Zorg ervoor dat u de resource-ID van de beheerde ID opslaat wanneer u deze toevoegt aan het Key Vault toegangs beleid.

## <a name="create-azure-key-vault"></a>Azure Key Vault maken

Een sleutelkluis maken. Zie [Azure Key Vault maken](../key-vault/secrets/quick-create-portal.md) voor specifieke stappen.

HDInsight ondersteunt alleen Azure Key Vault. Als u uw eigen sleutel kluis hebt, kunt u uw sleutels importeren in Azure Key Vault. Houd er rekening mee dat voor de sleutel kluis **voorlopig verwijderen** moet zijn ingeschakeld. Ga voor meer informatie over het importeren van bestaande sleutels naar [sleutels, geheimen en certificaten](../key-vault/about-keys-secrets-and-certificates.md).

## <a name="create-key"></a>Sleutel maken

1. Navigeer vanuit uw nieuwe sleutel kluis naar **instellingen**  >  **sleutels**  >  **+ genereren/importeren**.

    ![Een nieuwe sleutel in Azure Key Vault genereren](./media/disk-encryption/create-new-key.png "Een nieuwe sleutel in Azure Key Vault genereren")

1. Geef een naam op en selecteer **maken**. Het standaard **sleutel type** van **RSA**onderhouden.

    ![genereert sleutel naam](./media/disk-encryption/create-key.png "Sleutel naam genereren")

1. Wanneer u terugkeert naar de pagina **sleutels** , selecteert u de sleutel die u hebt gemaakt.

    ![sleutel kluis sleutel lijst](./media/disk-encryption/key-vault-key-list.png)

1. Selecteer de versie om de pagina met de **sleutel versie** te openen. Wanneer u uw eigen sleutel voor HDInsight-cluster versleuteling gebruikt, moet u de sleutel-URI opgeven. Kopieer de **sleutel-id** en sla deze ergens op totdat u klaar bent om uw cluster te maken.

    ![sleutel-id ophalen](./media/disk-encryption/get-key-identifier.png)

## <a name="create-access-policy"></a>Toegangs beleid maken

1. Ga vanuit de nieuwe sleutel kluis naar **instellingen**  >  **toegangs beleid**  >  **+ toegangs beleid toevoegen**.

    ![Nieuw toegangs beleid voor Azure Key Vault maken](./media/disk-encryption/key-vault-access-policy.png)

1. Geef op de pagina **toegangs beleid toevoegen** de volgende informatie op:

    |Eigenschap |Beschrijving|
    |---|---|
    |Sleutel machtigingen|Selecteer **ophalen**, **deterugloop sleutel**en **Terugloop sleutel**.|
    |Geheime machtigingen|Selecteer **ophalen**, **instellen**en **verwijderen**.|
    |Principal selecteren|Selecteer de door de gebruiker toegewezen beheerde identiteit die u eerder hebt gemaakt.|

    ![Selecteer de principal voor Azure Key Vault toegangs beleid instellen](./media/disk-encryption/azure-portal-add-access-policy.png)

1. Selecteer **Toevoegen**.

1. Selecteer **Opslaan**.

    ![Azure Key Vault toegangs beleid opslaan](./media/disk-encryption/add-key-vault-access-policy-save.png)

## <a name="create-cluster-with-customer-managed-key-disk-encryption"></a>Een cluster maken met door de klant beheerde sleutel schijf versleuteling

U bent nu klaar om een nieuw HDInsight-cluster te maken. Door de klant beheerde sleutel kan alleen worden toegepast op nieuwe clusters tijdens het maken van het cluster. Versleuteling kan niet worden verwijderd uit door de klant beheerde sleutel clusters en de door de klant beheerde sleutel kan niet worden toegevoegd aan bestaande clusters.

### <a name="using-the-azure-portal"></a>Azure Portal gebruiken

Geef tijdens het maken van het cluster de volledige **sleutel-id**op, met inbegrip van de sleutel versie. Bijvoorbeeld `https://contoso-kv.vault.azure.net/keys/myClusterKey/46ab702136bc4b229f8b10e8c2997fa4`. U moet ook de beheerde identiteit toewijzen aan het cluster en de sleutel-URI opgeven.

![Nieuw cluster maken](./media/disk-encryption/create-cluster-portal.png)

### <a name="using-azure-cli"></a>Azure CLI gebruiken

In het volgende voor beeld ziet u hoe u Azure CLI gebruikt om een nieuw Apache Spark-cluster te maken waarvoor schijf versleuteling is ingeschakeld. Zie [Azure cli AZ hdinsight Create](https://docs.microsoft.com/cli/azure/hdinsight?view=azure-cli-latest#az-hdinsight-create)voor meer informatie.

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

In het volgende voor beeld ziet u hoe u een Azure Resource Manager sjabloon gebruikt om een nieuw Apache Spark-cluster te maken waarvoor schijf versleuteling is ingeschakeld. Zie [Wat zijn arm-sjablonen?](https://docs.microsoft.com/azure/azure-resource-manager/templates/overview)voor meer informatie.

In dit voor beeld wordt Power shell gebruikt om de sjabloon aan te roepen.

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

De inhoud van de resource beheer sjabloon, `azuredeploy.json` :

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

## <a name="rotating-the-encryption-key"></a>De versleutelings sleutel draaien

Er zijn mogelijk scenario's waarin u mogelijk de versleutelings sleutels wilt wijzigen die door het HDInsight-cluster worden gebruikt nadat het is gemaakt. Dit kan eenvoudig zijn via de portal. Voor deze bewerking moet het cluster toegang hebben tot zowel de huidige sleutel als de beoogde nieuwe sleutel, anders kan de bewerking voor het draaien van de sleutel niet worden uitgevoerd.

### <a name="using-the-azure-portal"></a>Azure Portal gebruiken

Als u de sleutel wilt draaien, hebt u de URL van de basis sleutel kluis nodig. Als u dat hebt gedaan, gaat u naar de sectie eigenschappen van HDInsight-cluster in de portal en klikt u op de **sleutel wijzigen** onder URL voor de **schijf versleutelings sleutel**. Voer in de nieuwe sleutel-URL in en verzend de sleutel om deze te draaien.

![schijf versleutelings sleutel draaien](./media/disk-encryption/change-key.png)

### <a name="using-azure-cli"></a>Azure CLI gebruiken

In het volgende voor beeld ziet u hoe u de schijf versleutelings sleutel voor een bestaand HDInsight-cluster roteert. Zie [Azure cli AZ hdinsight Rotate-Disk-Encryption-Key](https://docs.microsoft.com/cli/azure/hdinsight?view=azure-cli-latest#az-hdinsight-rotate-disk-encryption-key)voor meer informatie.

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

* Zie [Wat is Azure Key Vault](../key-vault/general/overview.md)voor meer informatie over Azure Key Vault.
* [Overzicht van ENTER prise Security in azure HDInsight](./domain-joined/hdinsight-security-overview.md).
