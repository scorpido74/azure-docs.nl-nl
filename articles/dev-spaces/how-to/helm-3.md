---
title: Uw Azure Dev Spaces-cluster configureren voor het gebruik van Helm 3 (preview)
services: azure-dev-spaces
ms.date: 02/28/2020
ms.topic: conceptual
description: Meer informatie over het configureren van uw cluster voor dev Spaces voor het gebruik van helm 3
keywords: Azure dev Spaces, dev Spaces, docker, Kubernetes, azure, AKS, Azure Kubernetes service, containers
ms.openlocfilehash: dbccb2618fd5a27805261d60e7891d920e0bc372
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/28/2020
ms.locfileid: "79454292"
---
# <a name="configure-your-azure-dev-spaces-cluster-to-use-helm-3-preview"></a>Uw Azure Dev Spaces-cluster configureren voor het gebruik van Helm 3 (preview)

Azure dev Spaces maakt standaard gebruik van helm 2 voor het installeren van gebruikers services in dev Spaces op uw AKS-cluster. U kunt Azure dev Spaces inschakelen om helm 3 te gebruiken in plaats van helm 2 gebruikers services installeren in dev Spaces. Ongeacht de versie van helm Azure dev Spaces gebruikt om gebruikers services te installeren, kunt u de helm 2-of 3-client blijven gebruiken voor het beheren van uw eigen releases op hetzelfde cluster.

Wanneer u helm 3 inschakelt, gedragen Azure dev Spaces zich anders wanneer gebruikers services in dev Spaces op de volgende manieren worden geïnstalleerd:

* Tiller wordt niet meer geïmplementeerd in uw cluster in de *azds* -naam ruimte.
* Helm slaat release-informatie op in de naam ruimte waarin een service wordt geïnstalleerd in plaats van de *azds* -naam ruimte.
* Helm 3 release-informatie blijft in de naam ruimte waar een service is geïnstalleerd nadat een controller is verwijderd.
* U kunt rechtstreeks communiceren met een release die wordt beheerd door Azure dev-ruimten in uw cluster met behulp van de helm 3-client.

In deze hand leiding vindt u informatie over het inschakelen van helm 3 voor Azure dev Spaces voor het installeren van gebruikers services in dev Spaces.

> [!IMPORTANT]
> Deze functie is momenteel beschikbaar als preview-product. Previews worden voor u beschikbaar gesteld op voorwaarde dat u akkoord gaat met de [aanvullende gebruiksvoorwaarden](https://azure.microsoft.com/support/legal/preview-supplemental-terms/). Sommige aspecten van deze functionaliteit kunnen wijzigen voordat deze functionaliteit algemeen beschikbaar wordt.

## <a name="before-you-begin"></a>Voordat u begint

### <a name="prerequisites"></a>Vereisten

* Een Azure-abonnement. Als u geen Azure-abonnement hebt, kunt u een [gratis account](https://azure.microsoft.com/free)maken.
* [Azure CLI geïnstalleerd][azure-cli].

### <a name="register-the-helm3preview-preview-feature"></a>De preview-functie voor Helm3Preview registreren

Als u Azure dev Spaces wilt inschakelen voor het gebruik van helm 3 voor het installeren van gebruikers services in dev Spaces, moet u eerst de *Helm3Preview* -functie vlag inschakelen voor uw abonnement met behulp van de opdracht *AZ feature REGI ster* :

> [!WARNING]
> Met een AKS-cluster waarin u Azure dev Spaces inschakelt met de functie vlag *Helm3Preview* , wordt deze preview-ervaring gebruikt. Als u volledig ondersteunde Azure dev Spaces wilt blijven inschakelen op AKS-clusters, schakelt u geen preview-functies in voor productie abonnementen. Gebruik een afzonderlijk test-of ontwikkelings-Azure-abonnement voor het testen van preview-functies.

```azure-cli
az feature register --namespace Microsoft.DevSpaces --name Helm3Preview
```

Het duurt enkele minuten voordat de registratie is voltooid. Controleer de registratie status met behulp van de opdracht *AZ functie show* :

```azure-cli
az feature show --namespace Microsoft.DevSpaces --name Helm3Preview
```

Wanneer de *status* is *geregistreerd*, vernieuwt u de registratie van *micro soft. DevSpaces* met *AZ provider REGI ster*:

```azure-cli
az provider register --namespace Microsoft.DevSpaces
```

### <a name="limitations"></a>Beperkingen

De volgende beperkingen zijn van toepassing terwijl deze functie in preview is:

* U kunt deze functie niet gebruiken in AKS-clusters met bestaande workloads. U moet een nieuw AKS-cluster maken.

## <a name="create-your-cluster"></a>Uw cluster maken

Maak een nieuw AKS-cluster in een regio met deze preview-functie. Met de onderstaande opdrachten maakt u een resource groep met de naam *MyResourceGroup* en een nieuw AKS-cluster met de naam *MyAKS*:

```azure-cli
az group create --name MyResourceGroup --location eastus
az aks create -g MyResourceGroup -n MyAKS --location eastus --generate-ssh-keys
```

## <a name="enable-azure-dev-spaces"></a>Azure dev Spaces inschakelen

Gebruik de opdracht *gebruik-dev-Spaces* om ontwikkel ruimten in uw AKS-cluster in te scha kelen en de prompts te volgen. Met de onderstaande opdracht maakt u ontwikkel ruimten in het *MyAKS* -cluster in de groep *MyResourceGroup* en maakt u een standaard dev-ruimte.

```cmd
az aks use-dev-spaces -g MyResourceGroup -n MyAKS
```

## <a name="verify-dev-spaces-is-running-helm-3"></a>Controleren of dev Spaces wordt uitgevoerd helm 3

Controleer of de Tiller niet wordt uitgevoerd door de implementaties in de *azds* -naam ruimte weer te geven:

```cmd
kubectl get deployment -n azds
```

Bevestigen dat *Tiller wordt geïmplementeerd* , wordt niet uitgevoerd in de azds-naam ruimte. Bijvoorbeeld:

```console
$ kubectl get deployments -n azds
NAME                      READY   UP-TO-DATE   AVAILABLE   AGE
azds-webhook-deployment   2/2     2            2           39m
traefik                   1/1     1            1           39m
```

## <a name="next-steps"></a>Volgende stappen

Meer informatie over hoe Azure dev Spaces u helpt om complexere toepassingen te ontwikkelen in meerdere containers en hoe u samenwerkings ontwikkeling kunt vereenvoudigen door te werken met verschillende versies of vertakkingen van uw code in verschillende ruimten.

> [!div class="nextstepaction"]
> [Team ontwikkeling in azure dev Spaces][team-quickstart]


[azure-cli]: /cli/azure/install-azure-cli?view=azure-cli-latest
[team-quickstart]: ../quickstart-team-development.md