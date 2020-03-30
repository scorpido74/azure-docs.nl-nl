---
title: Clusterconfiguratie in Azure Kubernetes Services (AKS)
description: Meer informatie over het configureren van een cluster in Azure Kubernetes Service (AKS)
services: container-service
ms.topic: conceptual
ms.date: 03/12/2020
ms.openlocfilehash: 94f84beee2d7a76e48ac1470a0ce0b387929cc08
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79479161"
---
# <a name="configure-an-aks-cluster"></a>Een AKS-cluster configureren

Als onderdeel van het maken van een AKS-cluster moet u mogelijk uw clusterconfiguratie aanpassen aan uw behoeften. In dit artikel worden een aantal opties geïntroduceerd voor het aanpassen van uw AKS-cluster.

## <a name="os-configuration-preview"></a>BE-configuratie (voorbeeld)

AKS ondersteunt nu Ubuntu 18.04 als het node besturingssysteem (OS) in preview. Tijdens de preview-periode zijn zowel Ubuntu 16.04 als Ubuntu 18.04 beschikbaar.

U moet de volgende bronnen hebben geïnstalleerd:

- De Azure CLI, versie 2.2.0 of hoger
- De aks-preview 0.4.35 extensie

Als u de aks-preview 0.4.35-extensie of hoger wilt installeren, gebruikt u de volgende Azure CLI-opdrachten:

```azurecli
az extension add --name aks-preview
az extension list
```

Registreer `UseCustomizedUbuntuPreview` de functie:

```azurecli
az feature register --name UseCustomizedUbuntuPreview --namespace Microsoft.ContainerService
```

Het kan enkele minuten duren voordat de status wordt weergegeven als **Geregistreerd.** U de registratiestatus controleren met de opdracht [az-functielijst:](https://docs.microsoft.com/cli/azure/feature?view=azure-cli-latest#az-feature-list)

```azurecli
az feature list -o table --query "[?contains(name, 'Microsoft.ContainerService/UseCustomizedUbuntuPreview')].{Name:name,State:properties.state}"
```

Wanneer de status wordt weergegeven als `Microsoft.ContainerService` geregistreerd, vernieuwt u de registratie van de resourceprovider met behulp van de opdracht [AZ-providerregister:](https://docs.microsoft.com/cli/azure/provider?view=azure-cli-latest#az-provider-register)

```azurecli
az provider register --namespace Microsoft.ContainerService
```

Configureer het cluster om Ubuntu 18.04 te gebruiken wanneer het cluster wordt gemaakt. Gebruik `--aks-custom-headers` de vlag om de Ubuntu 18.04 in te stellen als het standaardbesturingssysteem.

```azure-cli
az aks create --name myAKSCluster --resource-group myResourceGroup --aks-custom-headers CustomizedUbuntu=aks-ubuntu-1804
```

Als u een gewoon Ubuntu 16.04-cluster wilt maken, `--aks-custom-headers` u dit doen door de aangepaste tag weg te laten.

## <a name="custom-resource-group-name"></a>Aangepaste naam resourcegroep

Wanneer u een Azure Kubernetes Service-cluster implementeert in Azure, wordt een tweede brongroep gemaakt voor de werknemersknooppunten. Aks geeft standaard een naam aan `MC_resourcegroupname_clustername_location`de brongroep knooppunt, maar u ook uw eigen naam opgeven.

Als u uw eigen naam van de brongroep wilt opgeven, installeert u de versie 0.3.2 of hoger van De Aks-Preview Azure CLI-extensie. Gebruik met de Azure `--node-resource-group` CLI `az aks create` de parameter van de opdracht om een aangepaste naam voor de resourcegroep op te geven. Als u een Azure Resource Manager-sjabloon gebruikt om een AKS-cluster `nodeResourceGroup` te implementeren, u de naam van de brongroep definiëren met behulp van de eigenschap.

```azurecli
az aks create --name myAKSCluster --resource-group myResourceGroup --node-resource-group myNodeResourceGroup
```

De secundaire brongroep wordt automatisch gemaakt door de Azure-resourceprovider in uw eigen abonnement. Houd er rekening mee dat u alleen de aangepaste naam van de resourcegroep opgeven wanneer het cluster wordt gemaakt. 

Houd er bij het werken met de brongroep knooppunt rekening mee dat u niet:

- Geef een bestaande resourcegroep op voor de knooppuntbrongroep.
- Geef een ander abonnement op voor de brongroep knooppunt.
- Wijzig de naam van de knooppuntbrongroep nadat het cluster is gemaakt.
- Geef namen op voor de beheerde resources in de brongroep knooppunt.
- Labels van beheerde resources in de brongroep knooppunt wijzigen of verwijderen in Azure.

## <a name="next-steps"></a>Volgende stappen

- Meer informatie `Kured` over het gebruik van [beveiligings- en kernelupdates op Linux-knooppunten](node-updates-kured.md) in uw cluster.
- Zie [Een AKS-cluster (Azure Kubernetes Service) upgraden](upgrade-cluster.md) voor meer informatie over het upgraden van uw cluster naar de nieuwste versie van Kubernetes.
- Zie de lijst [met veelgestelde vragen over AKS](faq.md) om antwoorden te vinden op enkele veelvoorkomende AKS-vragen.