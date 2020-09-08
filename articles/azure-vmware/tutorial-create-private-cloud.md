---
title: Zelfstudie - vSphere-cluster implementeren in Azure
description: Meer informatie over het implementeren van een vSphere-cluster in Azure met behulp van Azure VMware Solution
ms.topic: tutorial
ms.date: 09/07/2020
ms.openlocfilehash: 69a29a459ba283bb34169112ac2fa174ac6a14af
ms.sourcegitcommit: 8791f69d44150767807d215cafc4076f3ed43f9f
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 09/08/2020
ms.locfileid: "89512358"
---
# <a name="tutorial-deploy-an-azure-vmware-solution-private-cloud-in-azure"></a>Zelfstudie: Een Azure VMware Solution-privécloud implementeren in Azure

Met Azure VMware Solution kunt u een vSphere-cluster in Azure implementeren. De minimale initiële implementatie is drie hosts. Extra hosts kunnen een voor een worden toegevoegd, maximaal 16 hosts per cluster. 

Met Azure VMware Solution kunt u bij het starten uw privécloud niet met uw on-premises vCenter beheren. Daarom moet u aanvullende configuratie uitvoeren en verbinding maken met een lokaal vCenter-exemplaar, een virtueel netwerk en meer. Deze procedures en gerelateerde vereisten worden behandeld in deze zelfstudie.

In deze zelfstudie leert u het volgende:

> [!div class="checklist"]
> * Een Azure VMware Solution-privécloud maken
> * Controleer of de privécloud is geïmplementeerd

## <a name="prerequisites"></a>Vereisten

- Een Azure-account met een actief abonnement. [Gratis een account maken](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)
- De juiste beheerdersrechten en machtigingen voor het maken van een privécloud.
- Zorg ervoor dat de juiste netwerken zijn geconfigureerd zoals beschreven in [ Zelfstudie: Controlelijst voor netwerken](tutorial-network-checklist.md).

## <a name="register-the-resource-provider"></a>De resourceprovider registreren

[!INCLUDE [register-resource-provider-steps](includes/register-resource-provider-steps.md)]


## <a name="create-a-private-cloud"></a>Een privécloud maken

U kunt een Azure VMware Solution-privécloud maken met behulp van [Azure Portal](#azure-portal) of door de [Azure CLI](#azure-cli) te gebruiken.

### <a name="azure-portal"></a>Azure Portal

[!INCLUDE [create-avs-private-cloud-azure-portal](includes/create-avs-private-cloud-azure-portal-steps.md)]

### <a name="azure-cli"></a>Azure CLI

U kunt ook een Azure VMware Solution-privécloud maken met de Azure CLI met behulp van de Azure Cloud Shell in plaats van met Azure Portal. Het is een gratis interactieve shell met algemene Azure-hulpprogramma's die vooraf zijn geïnstalleerd en geconfigureerd voor gebruik met uw account. 

#### <a name="open-azure-cloud-shell"></a>Azure Cloud Shell openen

Als u Cloud Shell wilt openen, selecteert u **Proberen** in de rechterbovenhoek van een codeblok. U kunt Cloud Shell ook openen in een afzonderlijk browsertabblad door naar [https://shell.azure.com/bash](https://shell.azure.com/bash) te gaan. Klik op **Kopiëren** om de codeblokken te kopiëren, plak deze in Cloud Shell en druk vervolgens op **Enter** om de code uit te voeren.

#### <a name="create-a-resource-group"></a>Een resourcegroep maken

Een resourcegroep maken met de opdracht [az group create](/cli/azure/group). Een Azure-resourcegroep is een logische container waarin Azure-resources worden geïmplementeerd en beheerd. In het volgende voorbeeld wordt een resourcegroep met de naam *myResourceGroup* gemaakt op de locatie *eastus*:

```
azurecli-interactive
az group create --name myResourceGroup --location eastus
```

#### <a name="create-a-private-cloud"></a>Een privécloud maken

Geef een naam voor de resourcegroep op, een naam voor de privécloud, een locatie, de grootte van het cluster.

| Eigenschap  | Beschrijving  |
| --------- | ------------ |
| **-g** (Naam van resourcegroep)     | De naam van de resourcegroep voor uw privécloud-resources.        |
| **-n** (Naam van privécloud)     | De naam van uw Azure VMware Solution-privécloud.        |
| **--location**     | De locatie die voor uw privécloud wordt gebruikt.         |
| **--cluster-size**     | De grootte van de cluster. De minimumwaarde is 3.         |
| **--network-block**     | Het netwerkblok voor het CIDR IP-adres dat moet worden gebruikt voor uw privécloud. Het adresblok mag geen overlap vertonen met de in andere virtuele netwerken gebruikte adresblokken die zich in uw abonnement en on-premises netwerken bevinden.        |
| **--sku** | De SKU-waarde: AV36 |

```
azurecli-interactive
az vmware private-cloud create -g myResourceGroup -n myPrivateCloudName --location eastus --cluster-size 3 --network-block xx.xx.xx.xx/22 --sku AV36
```

## <a name="delete-a-private-cloud-azure-portal"></a>Een privécloud verwijderen (Azure Portal)

Als u een Azure VMware Solution-privécloud hebt die u niet meer nodig hebt, kunt u deze verwijderen. Wanneer u een privécloud verwijdert, worden alle clusters samen met al hun onderdelen verwijderd.

Als u dit wilt doen, gaat u naar uw privécloud in de Azure-portal en selecteert u **Verwijderen**. Bevestig op de pagina Bevestiging de naam van de privécloud en selecteer **Ja**.

> [!CAUTION]
> Het verwijderen van de privécloud is een onomkeerbare bewerking. Zodra de privécloud is verwijderd, kunnen de gegevens niet worden hersteld, omdat alle actieve workloads, onderdelen en alle persoonlijke cloudgegevens en configuratie-instellingen met inbegrip van openbare IP-adressen worden vernietigd. 

## <a name="next-steps"></a>Volgende stappen

In deze zelfstudie heeft u het volgende geleerd:

> [!div class="checklist"]
> * Een Azure VMware Solution-privécloud maken
> * Gecontroleerd of de privécloud is geïmplementeerd

Ga door naar de volgende zelfstudie voor meer informatie over het maken van een virtueel netwerk voor gebruik met uw privécloud als onderdeel van het instellen van lokaal beheer voor uw particuliere cloudclusters.

> [!div class="nextstepaction"]
> [Een virtueel netwerk maken](tutorial-configure-networking.md)
