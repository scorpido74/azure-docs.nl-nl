---
title: Een persoonlijk eind punt configureren (preview-versie)
titleSuffix: Azure Machine Learning
description: Persoonlijke Azure-koppeling gebruiken om veilig toegang te krijgen tot uw Azure Machine Learning-werk ruimte vanuit een virtueel netwerk.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.custom: how-to
ms.author: aashishb
author: aashishb
ms.reviewer: larryfr
ms.date: 07/28/2020
ms.openlocfilehash: 9ce139131e2c6cbfd73f9160b986d9886ae4844b
ms.sourcegitcommit: 3fb5e772f8f4068cc6d91d9cde253065a7f265d6
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 08/31/2020
ms.locfileid: "89181949"
---
# <a name="configure-azure-private-link-for-an-azure-machine-learning-workspace-preview"></a>Een persoonlijke Azure-koppeling configureren voor een Azure Machine Learning-werk ruimte (preview)

In dit document leert u hoe u een persoonlijke Azure-koppeling kunt gebruiken met uw Azure Machine Learning-werk ruimte. 

> [!IMPORTANT]
> Het gebruik van een persoonlijke Azure-koppeling met Azure Machine Learning werk ruimte bevindt zich momenteel in de open bare preview. Deze functionaliteit is alleen beschikbaar in de regio's **VS-Oost**, **VS Zuid-Centraal** en **VS West 2** . Deze preview-versie wordt zonder service level agreement gegeven en wordt niet aanbevolen voor productie werkbelastingen. Misschien worden bepaalde functies niet ondersteund of zijn de mogelijkheden ervan beperkt. Zie [Supplemental Terms of Use for Microsoft Azure Previews (Aanvullende gebruiksvoorwaarden voor Microsoft Azure-previews)](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) voor meer informatie.

Met de persoonlijke Azure-koppeling kunt u verbinding maken met uw werk ruimte met behulp van een persoonlijk eind punt. Het persoonlijke eind punt is een reeks privé-IP-adressen in uw virtuele netwerk. Vervolgens kunt u de toegang tot uw werk ruimte beperken tot alleen de privé-IP-adressen. Een persoonlijke koppeling helpt het risico van gegevens exfiltration te verminderen. Zie het artikel over een [persoonlijke Azure-koppeling](/azure/private-link/private-link-overview) voor meer informatie over privé-eind punten.

> [!IMPORTANT]
> Persoonlijke Azure-koppeling heeft geen invloed op Azure Control-vlak (beheer bewerkingen), zoals het verwijderen van de werk ruimte of het beheren van reken resources. Bijvoorbeeld maken, bijwerken of verwijderen van een berekenings doel. Deze bewerkingen worden normaal gesp roken uitgevoerd via het open bare Internet.
>
> Er kunnen problemen optreden bij het openen van het persoonlijke eind punt voor uw werk ruimte als u gebruikmaakt van Mozilla Firefox. Dit probleem kan betrekking hebben op DNS via HTTPS in Mozilla. We raden u aan micro soft Edge van Google Chrome als tijdelijke oplossing te gebruiken.

> [!TIP]
> Azure Machine Learning Compute-exemplaar kan worden gebruikt met een werk ruimte en een persoonlijk eind punt. Deze functie is momenteel beschikbaar in de open bare preview in de regio's **VS-Oost**, **VS Zuid-Centraal** en **VS West 2** .

## <a name="create-a-workspace-that-uses-a-private-endpoint"></a>Een werk ruimte maken die gebruikmaakt van een persoonlijk eind punt

> [!IMPORTANT]
> Op dit moment ondersteunen we alleen het inschakelen van een persoonlijk eind punt bij het maken van een nieuwe Azure Machine Learning-werk ruimte.

De sjabloon op [https://github.com/Azure/azure-quickstart-templates/tree/master/201-machine-learning-advanced](https://github.com/Azure/azure-quickstart-templates/tree/master/201-machine-learning-advanced) kan worden gebruikt om een werk ruimte met een persoonlijk eind punt te maken.

Zie [een Azure Resource Manager sjabloon gebruiken om een werk ruimte voor Azure machine learning te maken](how-to-create-workspace-template.md)voor meer informatie over het gebruik van deze sjabloon, met inbegrip van persoonlijke eind punten.

## <a name="using-a-workspace-over-a-private-endpoint"></a>Een werk ruimte gebruiken via een persoonlijk eind punt

Omdat de communicatie met de werk ruimte alleen is toegestaan vanuit het virtuele netwerk, moeten ontwikkel omgevingen die gebruikmaken van de werk ruimte lid zijn van het virtuele netwerk. Bijvoorbeeld een virtuele machine in het virtuele netwerk.

> [!IMPORTANT]
> Om te voor komen dat de verbinding tijdelijk wordt verbroken, raadt micro soft aan de DNS-cache te wissen op computers die verbinding maken met de werk ruimte nadat een persoonlijke koppeling is ingeschakeld. 

Raadpleeg de [virtual machines documentatie](/azure/virtual-machines/)voor meer informatie over Azure virtual machines.


## <a name="using-azure-storage"></a>Met behulp van Azure Storage

Als u het Azure Storage-account wilt beveiligen dat door uw werk ruimte wordt gebruikt, plaatst u het in het virtuele netwerk.

Zie [een opslag account voor uw werk ruimte gebruiken](how-to-enable-virtual-network.md#use-a-storage-account-for-your-workspace)voor meer informatie over het opslaan van het opslag account in het virtuele netwerk.

> [!WARNING]
> Azure Machine Learning biedt geen ondersteuning voor het gebruik van een Azure Storage account waarvoor een persoonlijke koppeling is ingeschakeld.

## <a name="using-azure-key-vault"></a>Azure Key Vault gebruiken

Als u de Azure Key Vault wilt beveiligen die wordt gebruikt door uw werk ruimte, kunt u deze in het virtuele netwerk plaatsen of een persoonlijke koppeling inschakelen.

Zie [een sleutel kluis-instantie gebruiken met uw werk ruimte](how-to-enable-virtual-network.md#key-vault-instance)voor meer informatie over het plaatsen van de sleutel kluis in het virtuele netwerk.

Zie voor meer informatie over het inschakelen van een persoonlijke koppeling voor de sleutel kluis [Key Vault integreren met persoonlijke Azure-koppeling](/azure/key-vault/private-link-service).

## <a name="using-azure-kubernetes-services"></a>Azure Kubernetes Services gebruiken

Als u de Azure Kubernetes-services die door uw werk ruimte worden gebruikt, wilt beveiligen, plaatst u deze in een virtueel netwerk. Zie [Azure Kubernetes Services gebruiken met uw werk ruimte](how-to-enable-virtual-network.md#aksvnet)voor meer informatie.

Azure Machine Learning ondersteunt nu het gebruik van een Azure Kubernetes-service waarvoor een persoonlijke koppeling is ingeschakeld.
Volg docs [hier](https://docs.microsoft.com/azure/aks/private-clusters) om een persoonlijk AKS-cluster te maken

## <a name="azure-container-registry"></a>Azure Container Registry

Zie [Azure container Registry gebruiken](how-to-enable-virtual-network.md#azure-container-registry)voor meer informatie over het beveiligen van Azure container registry in het virtuele netwerk.

> [!IMPORTANT]
> Als u een persoonlijke koppeling voor uw Azure Machine Learning-werk ruimte gebruikt en de Azure Container Registry voor uw werk ruimte in een virtueel netwerk hebt geplaatst, moet u ook de volgende Azure Resource Manager sjabloon Toep assen. Met deze sjabloon kan uw werk ruimte communiceren met ACR via de persoonlijke koppeling.

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
      "keyVaultArmId": {
      "type": "string"
      },
      "workspaceName": {
      "type": "string"
      },
      "containerRegistryArmId": {
      "type": "string"
      },
      "applicationInsightsArmId": {
      "type": "string"
      },
      "storageAccountArmId": {
      "type": "string"
      },
      "location": {
      "type": "string"
      }
  },
  "resources": [
      {
      "type": "Microsoft.MachineLearningServices/workspaces",
      "apiVersion": "2019-11-01",
      "name": "[parameters('workspaceName')]",
      "location": "[parameters('location')]",
      "identity": {
          "type": "SystemAssigned"
      },
      "sku": {
          "tier": "enterprise",
          "name": "enterprise"
      },
      "properties": {
          "sharedPrivateLinkResources":
  [{"Name":"Acr","Properties":{"PrivateLinkResourceId":"[concat(parameters('containerRegistryArmId'), '/privateLinkResources/registry')]","GroupId":"registry","RequestMessage":"Approve","Status":"Pending"}}],
          "keyVault": "[parameters('keyVaultArmId')]",
          "containerRegistry": "[parameters('containerRegistryArmId')]",
          "applicationInsights": "[parameters('applicationInsightsArmId')]",
          "storageAccount": "[parameters('storageAccountArmId')]"
      }
      }
  ]
}
```

## <a name="next-steps"></a>Volgende stappen

Zie het artikel over [zakelijke beveiliging](concept-enterprise-security.md) voor meer informatie over het beveiligen van uw Azure machine learning-werk ruimte.