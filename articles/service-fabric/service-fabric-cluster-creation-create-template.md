---
title: Een clustersjabloon Azure Service Fabric maken
description: Meer informatie over het maken van een resourcemanagersjabloon voor een cluster van Servicefabric. Configureer beveiliging, Azure Key Vault en Azure Active Directory (Azure AD) voor clientverificatie.
ms.topic: conceptual
ms.date: 08/16/2018
ms.openlocfilehash: 6cf0f9c3b8b54db7bd27ec8dd9c9d59d849c74cc
ms.sourcegitcommit: 7d8158fcdcc25107dfda98a355bf4ee6343c0f5c
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/09/2020
ms.locfileid: "80985368"
---
# <a name="create-a-service-fabric-cluster-resource-manager-template"></a>Een sjabloon voor resourcebeheer van servicefabricmaken

Een [Azure Service Fabric-cluster](service-fabric-deploy-anywhere.md) is een met een netwerk verbonden set virtuele machines waarin uw microservices worden geïmplementeerd en beheerd. Een Cluster servicefabric dat in Azure wordt uitgevoerd, is een Azure-bron en wordt geïmplementeerd, beheerd en bewaakt met behulp van Resourcebeheer.  In dit artikel wordt beschreven hoe u een resourcemanagersjabloon maakt voor een cluster van Servicefabric dat in Azure wordt uitgevoerd.  Wanneer de sjabloon is voltooid, u [het cluster implementeren op Azure.](service-fabric-cluster-creation-via-arm.md)

Clusterbeveiliging wordt geconfigureerd wanneer het cluster voor het eerst is ingesteld en kan later niet worden gewijzigd. Lees voor het instellen van een cluster [beveiligingsscenario's voor servicefabric.][service-fabric-cluster-security] In Azure gebruikt Service Fabric het X509-certificaat om uw cluster en de eindpunten te beveiligen, clients te verifiëren en gegevens te versleutelen. Azure Active Directory wordt ook aanbevolen om de toegang tot beheereindpunten te beveiligen. Azure AD-tenants en -gebruikers moeten worden gemaakt voordat het cluster wordt gemaakt.  Lees [Azure AD instellen om clients te verifiëren voor](service-fabric-cluster-creation-setup-aad.md)meer informatie.

Voordat u een productiecluster implementeert om productieworkloads uit te voeren, moet u eerst de [checklist productiegereedheid](service-fabric-production-readiness-checklist.md)lezen.


[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="create-the-resource-manager-template"></a>Het Resource Manager-sjabloon maken
Voorbeeldvan Resource Beheer-sjablonen zijn beschikbaar in de [Azure-voorbeelden op GitHub.](https://github.com/Azure-Samples/service-fabric-cluster-templates) Deze sjablonen kunnen worden gebruikt als uitgangspunt voor uw clustersjabloon.

In dit artikel wordt gebruik gemaakt van de sjabloon voor beveiligde clustervoorbeelden en sjabloonparameters [met vijf nodes.][service-fabric-secure-cluster-5-node-1-nodetype] Download *azuredeploy.json* en *azuredeploy.parameters.json* naar uw computer en open beide bestanden in uw favoriete teksteditor.

> [!NOTE]
> Voor nationale clouds (Azure Government, Azure China, Azure `fabricSettings` Germany) moet `AADLoginEndpoint` `AADTokenEndpointFormat` u `AADCertEndpointFormat`ook het volgende aan uw sjabloon toevoegen: , en .

## <a name="add-certificates"></a>Certificaten toevoegen
U voegt certificaten toe aan een clusterresourcemanagersjabloon door te verwijzen naar de sleutelkluis die de certificaatsleutels bevat. Voeg deze parameters en waarden voor sleutelwaarden toe aan een sjabloonparametersbestand resourcebeheer *(azuredeploy.parameters.json).*

### <a name="add-all-certificates-to-the-virtual-machine-scale-set-osprofile"></a>Alle certificaten toevoegen aan de virtuele machine schaal set osProfile
Elk certificaat dat in het cluster is geïnstalleerd, moet worden geconfigureerd in het gedeelte **osProfile** van de schaalsetbron (Microsoft.Compute/virtualMachineScaleSets). Met deze actie wordt de resourceprovider opgedragen het certificaat op de VM's te installeren. Deze installatie omvat zowel het clustercertificaat als alle toepassingsbeveiligingscertificaten die u voor uw toepassingen wilt gebruiken:

```json
{
  "apiVersion": "[variables('vmssApiVersion')]",
  "type": "Microsoft.Compute/virtualMachineScaleSets",
  ...
  "properties": {
    ...
    "osProfile": {
      ...
      "secrets": [
        {
          "sourceVault": {
            "id": "[parameters('sourceVaultValue')]"
          },
          "vaultCertificates": [
            {
              "certificateStore": "[parameters('clusterCertificateStorevalue')]",
              "certificateUrl": "[parameters('clusterCertificateUrlValue')]"
            },
            {
              "certificateStore": "[parameters('applicationCertificateStorevalue')",
              "certificateUrl": "[parameters('applicationCertificateUrlValue')]"
            },
            ...
          ]
        }
      ]
    }
  }
}
```

### <a name="configure-the-service-fabric-cluster-certificate"></a>Het clustercertificaat Servicefabric configureren

Het clusterverificatiecertificaat moet zijn geconfigureerd in zowel de clusterbron Service Fabric (Microsoft.ServiceFabric/clusters) als de servicestructuurextensie voor virtuele machineschaalsets in de bron voor de virtuele machineschaalset. Met deze regeling kan de serviceprovider servicefabric-bron deze configureren voor gebruik voor clusterverificatie en serververificatie voor beheereindpunten.

#### <a name="add-the-certificate-information-the-virtual-machine-scale-set-resource"></a>De certificaatgegevens toevoegen de bron voor de virtuele machineschaalset

```json
{
  "apiVersion": "[variables('vmssApiVersion')]",
  "type": "Microsoft.Compute/virtualMachineScaleSets",
  ...
  "properties": {
    ...
    "virtualMachineProfile": {
      "extensionProfile": {
        "extensions": [
          {
            "name": "[concat('ServiceFabricNodeVmExt_',variables('vmNodeType0Name'))]",
            "properties": {
              ...
              "settings": {
                ...
                "certificate": {
                  "commonNames": ["[parameters('certificateCommonName')]"],
                  "x509StoreName": "[parameters('clusterCertificateStoreValue')]"
                },
                ...
              }
            }
          }
        ]
      }
    }
  }
}
```

#### <a name="add-the-certificate-information-to-the-service-fabric-cluster-resource"></a>De certificaatgegevens toevoegen aan de clusterbron ServiceFabric

```json
{
  "apiVersion": "2018-02-01",
  "type": "Microsoft.ServiceFabric/clusters",
  "name": "[parameters('clusterName')]",
  "location": "[parameters('clusterLocation')]",
  "dependsOn": [
    "[concat('Microsoft.Storage/storageAccounts/', variables('supportLogStorageAccountName'))]"
  ],
  "properties": {
    "certificateCommonNames": {
        "commonNames": [
        {
            "certificateCommonName": "[parameters('certificateCommonName')]",
            "certificateIssuerThumbprint": ""
        }
        ],
        "x509StoreName": "[parameters('certificateStoreValue')]"
    },
    ...
  }
}
```

## <a name="add-azure-ad-configuration-to-use-azure-ad-for-client-access"></a>Azure Active Directory-configuratie toevoegen voor het gebruik van Azure Active Directory voor clienttoegang

U voegt de Azure AD-configuratie toe aan een clusterresourcebeheersjabloon door te verwijzen naar de sleutelkluis die de certificaatsleutels bevat. Voeg deze Azure AD-parameters en -waarden toe aan een bestand met sjabloonparameters resourcebeheer *(azuredeploy.parameters.json).* 

> [!NOTE]
> Op Linux moeten Azure AD-tenants en -gebruikers worden gemaakt voordat u het cluster maakt.  Lees [Azure AD instellen om clients te verifiëren voor](service-fabric-cluster-creation-setup-aad.md)meer informatie.

```json
{
  "apiVersion": "2018-02-01",
  "type": "Microsoft.ServiceFabric/clusters",
  "name": "[parameters('clusterName')]",
  ...
  "properties": {
    "certificateCommonNames": {
        "commonNames": [
        {
            "certificateCommonName": "[parameters('certificateCommonName')]",
            "certificateIssuerThumbprint": ""
        }
        ],
        "x509StoreName": "[parameters('certificateStoreValue')]"
    },
    ...
    "azureActiveDirectory": {
      "tenantId": "[parameters('aadTenantId')]",
      "clusterApplication": "[parameters('aadClusterApplicationId')]",
      "clientApplication": "[parameters('aadClientApplicationId')]"
    },
    ...
  }
}
```

## <a name="populate-the-parameter-file-with-the-values"></a>Het parameterbestand vullen met de waarden

Gebruik ten slotte de uitvoerwaarden uit de opdrachten Sleutelkluis en Azure AD PowerShell om het parametersbestand in te vullen.

Als u van plan bent de RM PowerShell-modules van Azure-servicefabric te gebruiken, hoeft u de clustercertificaatgegevens niet in te vullen. Als u wilt dat het systeem het zelf ondertekende certificaat voor clusterbeveiliging genereert, houdt u ze gewoon als null. 

> [!NOTE]
> Als de RM-modules deze lege parameterwaarden kunnen ophalen en invullen, komen de parametersnamen veel overeen met de onderstaande namen

```json
"clusterCertificateThumbprint": {
    "value": ""
},
"certificateCommonName": {
    "value": ""
},
"clusterCertificateUrlValue": {
    "value": ""
},
"sourceVaultvalue": {
    "value": ""
},
```

Als u toepassingscertificaten gebruikt of een bestaand cluster gebruikt dat u naar de sleutelkluis hebt geüpload, moet u deze informatie krijgen en deze invullen.

De RM-modules hebben niet de mogelijkheid om de Azure AD-configuratie voor u te genereren, dus als u van plan bent de Azure AD te gebruiken voor clienttoegang, moet u deze invullen.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        ...
        "clusterCertificateStoreValue": {
            "value": "My"
        },
        "clusterCertificateThumbprint": {
            "value": "<thumbprint>"
        },
        "clusterCertificateUrlValue": {
            "value": "https://myvault.vault.azure.net:443/secrets/myclustercert/4d087088df974e869f1c0978cb100e47"
        },
        "applicationCertificateStorevalue": {
            "value": "My"
        },
        "applicationCertificateUrlValue": {
            "value": "https://myvault.vault.azure.net:443/secrets/myapplicationcert/2e035058ae274f869c4d0348ca100f08"
        },
        "sourceVaultvalue": {
            "value": "/subscriptions/<guid>/resourceGroups/mycluster-keyvault/providers/Microsoft.KeyVault/vaults/myvault"
        },
        "aadTenantId": {
            "value": "<guid>"
        },
        "aadClusterApplicationId": {
            "value": "<guid>"
        },
        "aadClientApplicationId": {
            "value": "<guid>"
        },
        ...
    }
}
```

## <a name="test-your-template"></a>Uw sjabloon testen
Gebruik de volgende PowerShell-opdracht om de sjabloon Resourcebeheer te testen met een parameterbestand:

```powershell
Test-AzResourceGroupDeployment -ResourceGroupName "myresourcegroup" -TemplateFile .\azuredeploy.json -TemplateParameterFile .\azuredeploy.parameters.json
```

In het geval dat u problemen tegenkomt en cryptische berichten ontvangt, gebruikt u "-Debug" als optie.

```powershell
Test-AzResourceGroupDeployment -ResourceGroupName "myresourcegroup" -TemplateFile .\azuredeploy.json -TemplateParameterFile .\azuredeploy.parameters.json -Debug
```

In het volgende diagram ziet u waar uw sleutelkluis en Azure AD-configuratie passen in uw Resource Manager-sjabloon.

![Afhankelijkheidskaart resourcebeheer][cluster-security-arm-dependency-map]

## <a name="next-steps"></a>Volgende stappen
Nu u een sjabloon voor uw cluster hebt, leest u hoe [u het cluster implementeert naar Azure.](service-fabric-cluster-creation-via-arm.md)  Als u dit nog niet hebt gedaan, leest u de [controlelijst productiegereedheid](service-fabric-production-readiness-checklist.md) voordat u een productiecluster implementeert.

Zie voor meer informatie over de syntaxis en eigenschappen van JSON voor de resources die in dit artikel zijn geïmplementeerd:

* [Microsoft.ServiceFabric/clusters](/azure/templates/microsoft.servicefabric/clusters)
* [Microsoft.Storage/storageAccounts](/azure/templates/microsoft.storage/storageaccounts)
* [Microsoft.Network/virtualNetworks](/azure/templates/microsoft.network/virtualnetworks)
* [Microsoft.Network/publicIPAddresses](/azure/templates/microsoft.network/publicipaddresses)
* [Microsoft.Network/loadBalancers](/azure/templates/microsoft.network/loadbalancers)
* [Microsoft.Compute/virtualMachineScaleSets](/azure/templates/microsoft.compute/virtualmachinescalesets)

<!-- Links -->
[service-fabric-cluster-security]: service-fabric-cluster-security.md
[service-fabric-secure-cluster-5-node-1-nodetype]: https://github.com/Azure-Samples/service-fabric-cluster-templates/tree/master/5-VM-Windows-1-NodeTypes-Secure
[resource-group-template-deploy]: https://azure.microsoft.com/documentation/articles/resource-group-template-deploy/

<!-- Images -->
[cluster-security-arm-dependency-map]: ./media/service-fabric-cluster-creation-create-template/cluster-security-arm-dependency-map.png
