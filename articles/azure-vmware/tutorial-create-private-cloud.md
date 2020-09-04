---
title: Zelfstudie - vSphere-cluster implementeren in Azure
description: Meer informatie over het implementeren van een vSphere-cluster in Azure met behulp van Azure VMware Solution
ms.topic: tutorial
ms.date: 08/21/2020
ms.openlocfilehash: 8aeedeeb785f149239f2bf9a4b58a18ec8bfeb77
ms.sourcegitcommit: 62717591c3ab871365a783b7221851758f4ec9a4
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 08/22/2020
ms.locfileid: "88750489"
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

Als u Azure VMware Solution wilt gebruiken, moet u de resourceprovider eerst registreren bij uw abonnement.

```
azurecli-interactive
az provider register -n Microsoft.AVS --subscription <your subscription ID>
```

Zie [Azure-resourceproviders en -typen](../azure-resource-manager/management/resource-providers-and-types.md) voor meer manieren om de Azure Synapse-resourceprovider te registeren.


## <a name="create-a-private-cloud"></a>Een privécloud maken

U kunt een Azure VMware Solution-privécloud maken met behulp van [Azure Portal](#azure-portal) of door de [Azure CLI](#azure-cli) te gebruiken.

### <a name="azure-portal"></a>Azure Portal

1. Meld u aan bij de [Azure-portal](https://portal.azure.com).

1. Selecteer **Een nieuwe resource maken**. Typ in het tekstvak **Marketplace doorzoeken** `Azure VMware Solution` en selecteer **Azure VMware Solution** uit de lijst. Selecteer **Maken** in het venster **Azure VMware Solution**

1. Voer op het tabblad **Basisinstellingen** waarden in voor de velden. De volgende tabel geeft de eigenschappen weer voor de velden.

   | Veld   | Waarde  |
   | ---| --- |
   | **Abonnement** | Het abonnement dat u wilt gebruiken voor de implementatie.|
   | **Resourcegroep** | De resourcegroep voor uw privécloud-resources. |
   | **Locatie** | Selecteer een locatie, zoals **VS-Oost**.|
   | **Resourcenaam** | De naam van uw Azure VMware Solution-privécloud. |
   | **SKU** | Selecteer de volgende SKU-waarde: AV36 |
   | **Hosts** | Dit is het aantal hosts dat aan het privécloudcluster moet worden toegevoegd. De standaardwaarde is 3, maar kan na de implementatie worden verhoogd of verlaagd.  |
   | **Beheerderswachtwoord van vCenter** | Voer een wachtwoord voor de cloudbeheerder in. |
   | **Manager-wachtwoord van NSX-T** | Voer een NSX-T-beheerderswachtwoord in. |
   | **Adres blokkeren** | Voer een IP-adresblok in voor het CIDR-netwerk voor de privécloud, zoals 10.175.0.0/22. |

   :::image type="content" source="./media/tutorial-create-private-cloud/create-private-cloud.png" alt-text="Voer op het tabblad Basisinstellingen waarden in voor de velden." border="true":::

1. Als dit gereed is, selecteert u **Controleren + maken**. Controleer de ingevoerde gegevens in het volgende scherm. Als de gegevens juist zijn, selecteert u **Maken**.

   > [!NOTE]
   > Deze stap duurt ongeveer twee uur. 

1. Controleer of de implementatie is gelukt. Ga naar de resourcegroep die u hebt gemaakt en selecteer uw privécloud.  U ziet de status **Geslaagd** wanneer de implementatie is voltooid. 

   :::image type="content" source="./media/tutorial-create-private-cloud/validate-deployment.png" alt-text="Controleer of de implementatie is gelukt." border="true":::

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
