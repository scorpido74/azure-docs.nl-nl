---
title: Uw azure dev spaces-cluster configureren om Helm 3 (voorbeeld) te gebruiken
services: azure-dev-spaces
ms.date: 02/28/2020
ms.topic: conceptual
description: Meer informatie over het configureren van uw cluster Dev Spaces om Helm 3 te gebruiken
keywords: Azure Dev Spaces, Dev Spaces, Docker, Kubernetes, Azure, AKS, Azure Kubernetes Service, containers
ms.openlocfilehash: dbccb2618fd5a27805261d60e7891d920e0bc372
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79454292"
---
# <a name="configure-your-azure-dev-spaces-cluster-to-use-helm-3-preview"></a>Uw azure dev spaces-cluster configureren om Helm 3 (voorbeeld) te gebruiken

Azure Dev Spaces gebruikt Helm 2 standaard om gebruikersservices te installeren in dev-ruimten op uw AKS-cluster. U Azure Dev Spaces inschakelen om Helm 3 te gebruiken in plaats van Helm 2 die gebruikersservices installeert in dev-ruimten. Ongeacht de versie die Helm Azure Dev Spaces gebruikt om gebruikersservices te installeren, u de Helm 2- of 3-client blijven gebruiken om uw eigen releases op hetzelfde cluster te beheren.

Wanneer u Helm 3 inschakelt, gedraagt Azure Dev Spaces zich anders wanneer u gebruikersservices in dev-ruimten op de volgende manieren installeert:

* Tiller wordt niet meer geïmplementeerd op uw cluster in de *naamruimte van azds.*
* Helm slaat informatie vrij in de naamruimte waar een service is geïnstalleerd in plaats van de *azds-naamruimte.*
* Stuur 3 release informatie blijft in de naamruimte waar een service is geïnstalleerd nadat een controller is verwijderd.
* U rechtstreeks communiceren met elke release die wordt beheerd door Azure Dev Spaces op uw cluster met behulp van de Helm 3-client.

In deze handleiding leert u hoe u Helm 3 voor Azure Dev Spaces inschakelt om gebruikersservices in dev-ruimten te installeren.

> [!IMPORTANT]
> Deze functie is momenteel beschikbaar als preview-product. Previews worden voor u beschikbaar gesteld op voorwaarde dat u akkoord gaat met de [aanvullende gebruiksvoorwaarden](https://azure.microsoft.com/support/legal/preview-supplemental-terms/). Sommige aspecten van deze functionaliteit kunnen wijzigen voordat deze functionaliteit algemeen beschikbaar wordt.

## <a name="before-you-begin"></a>Voordat u begint

### <a name="prerequisites"></a>Vereisten

* Een Azure-abonnement. Als u geen Azure-abonnement hebt, u een [gratis account](https://azure.microsoft.com/free)maken.
* [Azure CLI geïnstalleerd][azure-cli].

### <a name="register-the-helm3preview-preview-feature"></a>De helm3Preview-preview-functie registreren

Als u Azure Dev Spaces wilt inschakelen om Helm 3 te gebruiken voor het installeren van gebruikersservices in dev-ruimten, schakelt u eerst de *Helm3Preview-functievlag* in op uw abonnement met de opdracht *AZ-functieregister:*

> [!WARNING]
> Elk AKS-cluster waarop u Azure Dev Spaces inschakelt met de *Helm3Preview-functievlag,* gebruikt deze voorbeeldervaring. Als u volledig ondersteunde Azure Dev Spaces op AKS-clusters wilt blijven inschakelen, schakelt u geen voorbeeldfuncties in op productieabonnementen. Gebruik een apart Azure-abonnement voor het testen van preview-functies.

```azure-cli
az feature register --namespace Microsoft.DevSpaces --name Helm3Preview
```

Het duurt een paar minuten voordat de registratie is voltooid. Controleer de registratiestatus met de opdracht *AZ-functie toon:*

```azure-cli
az feature show --namespace Microsoft.DevSpaces --name Helm3Preview
```

Wanneer de *status* is *geregistreerd,* vernieuwt u de registratie van *Microsoft.DevSpaces* met behulp van *het AZ-providerregister:*

```azure-cli
az provider register --namespace Microsoft.DevSpaces
```

### <a name="limitations"></a>Beperkingen

De volgende beperkingen zijn van toepassing terwijl deze functie in een voorbeeld staat:

* U deze functie niet gebruiken op AKS-clusters met bestaande workloads. U moet een nieuw AKS-cluster maken.

## <a name="create-your-cluster"></a>Uw cluster maken

Maak een nieuw AKS-cluster in een regio met deze voorbeeldfunctie. Met de onderstaande opdrachten wordt een resourcegroep met de naam *MyResourceGroup* en een nieuw AKS-cluster met de naam *MyAKS*gemaakt:

```azure-cli
az group create --name MyResourceGroup --location eastus
az aks create -g MyResourceGroup -n MyAKS --location eastus --generate-ssh-keys
```

## <a name="enable-azure-dev-spaces"></a>Azure Dev-ruimten inschakelen

Gebruik de opdracht *use-dev-spaces* om Dev Spaces in te schakelen op uw AKS-cluster en volg de aanwijzingen. Met de opdracht Dev Spaces op het *MyAKS-cluster* in de groep *MyResourceGroup* wordt een standaarddev-ruimte gemaakt.

```cmd
az aks use-dev-spaces -g MyResourceGroup -n MyAKS
```

## <a name="verify-dev-spaces-is-running-helm-3"></a>Controleer of Dev Spaces roer 3 draait

Controleer of de helmstok niet wordt uitgevoerd door de implementaties in de naamruimte *van de AZD's* op te sommen:

```cmd
kubectl get deployment -n azds
```

Bevestig *dat tiller-deploy* niet wordt uitgevoerd in de naamruimte van de AZDs. Bijvoorbeeld:

```console
$ kubectl get deployments -n azds
NAME                      READY   UP-TO-DATE   AVAILABLE   AGE
azds-webhook-deployment   2/2     2            2           39m
traefik                   1/1     1            1           39m
```

## <a name="next-steps"></a>Volgende stappen

Ontdek hoe Azure Dev Spaces u helpt complexere toepassingen te ontwikkelen voor meerdere containers en hoe u de samenwerking vereenvoudigen door te werken met verschillende versies of branches van uw code in verschillende ruimten.

> [!div class="nextstepaction"]
> [Teamontwikkeling in Azure Dev Spaces][team-quickstart]


[azure-cli]: /cli/azure/install-azure-cli?view=azure-cli-latest
[team-quickstart]: ../quickstart-team-development.md