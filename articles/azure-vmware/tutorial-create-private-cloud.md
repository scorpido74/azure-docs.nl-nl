---
title: Zelf studie-een vSphere-cluster implementeren in azure
description: Meer informatie over het implementeren van een vSphere-cluster in azure met behulp van de Azure VMWare-oplossing (AVS)
ms.topic: tutorial
ms.date: 05/04/2020
ms.openlocfilehash: 712be25acf5984a4bcdf95ad70e0ccfa660c06bc
ms.sourcegitcommit: 11572a869ef8dbec8e7c721bc7744e2859b79962
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 05/05/2020
ms.locfileid: "82838791"
---
# <a name="tutorial-deploy-an-avs-private-cloud-in-azure"></a>Zelf studie: een privécloud in azure implementeren

Met de Azure VMware-oplossing (AVS) hebt u de mogelijkheid om een vSphere-cluster in azure te implementeren. De minimale initiële implementatie is drie hosts. Extra hosts kunnen een voor een worden toegevoegd, Maxi maal 16 hosts per cluster. 

Omdat AVS u niet in staat stelt om uw privécloud met uw on-premises vCenter te beheren bij het starten, moet u aanvullende configuratie van en verbinding maken met een lokaal vCenter-exemplaar, een virtueel netwerk en meer. Deze procedures en gerelateerde vereisten worden behandeld in deze reeks zelf studies.

In deze zelfstudie leert u het volgende:

> [!div class="checklist"]
> * Een privécloud maken
> * Controleer of de privécloud is geïmplementeerd

## <a name="prerequisites"></a>Vereisten

- Een Azure-account met een actief abonnement. [Maak gratis een account](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
- De juiste beheerders rechten en machtigingen voor het maken van een privécloud.
- Zorg ervoor dat de juiste netwerken zijn geconfigureerd zoals beschreven in [zelf studie: netwerk controlelijst](tutorial-network-checklist.md).

## <a name="register-the-resource-provider"></a>De resourceprovider registreren

Als u de Azure VMWare-oplossing wilt gebruiken, moet u eerst de resource provider registreren. In het volgende voor beeld wordt de resource provider geregistreerd bij uw abonnement.

```azurecli-interactive
az provider register -n Microsoft.VMwareVirtustream --subscription <your subscription ID>
```

Zie [Azure-resource providers en-typen](../azure-resource-manager/management/resource-providers-and-types.md)voor meer manieren om de resource provider te registreren.

## <a name="sign-in-to-the-azure-portal"></a>Aanmelden bij Azure Portal

Meld u aan bij [Azure Portal](https://portal.azure.com).

## <a name="create-a-private-cloud"></a>Een privécloud maken

U kunt een privécloud maken met behulp van de [Azure Portal](#azure-portal) of met behulp van de [Azure cli](#azure-cli).

### <a name="azure-portal"></a>Azure-portal

Selecteer in de Azure Portal **+ een nieuwe resource maken**. Typ `Azure VMware Solution`in het tekstvak **Marketplace doorzoeken** en selecteer **Azure VMware-oplossing** in de lijst. Selecteer in het venster **Azure VMware** -oplossing **maken**

Voer op het tabblad **basis beginselen** waarden in voor de velden. De volgende tabel bevat een gedetailleerde lijst met eigenschappen.

| Veld   | Waarde  |
| ---| --- |
| **Abonnement** | Het abonnement dat u wilt gebruiken voor de implementatie.|
| **Resourcegroep** | De resource groep voor uw privécloud-resources. |
| **Locatie** | Selecteer een locatie, zoals **VS-Oost**.|
| **Resourcenaam** | De naam van de privécloud van uw AVS. |
| **SKU** | Selecteer de juiste SKU. Dit zijn de beschik bare waarden: |
| **Hosts** | Dit is het aantal hosts dat aan het privécloud moet worden toegevoegd. De standaard waarde is 3. Deze waarde kan na de implementatie worden verhoogd of verlaagd.  |
| **vCenter-beheerders wachtwoord** | Voer een wacht woord voor de Cloud beheerder in. |
| **NSX-T-beheer wachtwoord** | Voer een NSX-T-beheerders wachtwoord in. |
| **Adres blok** | Voer een IP-adres blok in voor het CIDR-netwerk voor de privécloud. Een voor beeld is 10.175.0.0/22. |

:::image type="content" source="./media/tutorial-create-private-cloud/create-private-cloud.png" alt-text="een privécloud maken" border="true":::

Wanneer u klaar bent, selecteert u **controleren + maken**. Controleer de ingevoerde gegevens in het volgende scherm. Als de gegevens juist zijn, selecteert u **maken**.

> [!NOTE]
> Deze stap neemt ongeveer twee uur in beslag. 

### <a name="azure-cli"></a>Azure CLI

U kunt ook de Azure CLI gebruiken om een privécloud te maken in Azure. Als u dit wilt doen, kunt u de Azure Cloud Shell gebruiken. in de volgende stappen ziet u hoe u dit doet.

#### <a name="open-azure-cloud-shell"></a>Azure Cloud Shell openen

Azure Cloud Shell is een gratis interactieve shell waarmee u de stappen in dit artikel kunt uitvoeren. In deze shell zijn algemene Azure-hulpprogramma's vooraf geïnstalleerd en geconfigureerd voor gebruik met uw account.

Als u Cloud Shell wilt openen, selecteert u **Proberen** in de rechterbovenhoek van een codeblok. U kunt Cloud Shell ook openen in een afzonderlijk browsertabblad door naar https://shell.azure.com/bash te gaan. Selecteer **kopiëren** om de blokken code te kopiëren, plak deze in het Cloud shell en druk op **Enter** om het programma uit te voeren.

#### <a name="create-a-resource-group"></a>Een resourcegroep maken

Een resourcegroep maken met de opdracht [az group create](/cli/azure/group). Een Azure-resourcegroep is een logische container waarin Azure-resources worden geïmplementeerd en beheerd. In het volgende voor beeld wordt een resource groep met de naam *myResourceGroup* gemaakt op de locatie *eastus* :

```azurecli-interactive
az group create --name myResourceGroup --location eastus
```

#### <a name="create-a-private-cloud"></a>Een privécloud maken

Als u een privécloud wilt maken, moet u een naam voor de resource groep opgeven, een naam voor de privécloud, een locatie, de grootte van het cluster


|Eigenschap  |Beschrijving  |
|---------|---------|
|Naam van resource groep     | De naam van de resource groep waarnaar u de privécloud implementeert.        |
|Naam privécloud     | De naam voor de privécloud.        |
|Locatie     | De locatie die wordt gebruikt voor de privécloud         |
|Cluster grootte     | De grootte van het cluster. De minimum waarde is 3.         |
|Netwerk blok     | Het CIDR-bereik dat moet worden gebruikt voor de privécloud. Het wordt aanbevolen dat deze uniek is voor uw on-premises omgeving en uw Azure-omgeving.        |

```azurecli-interactive
az vmware private-cloud create -g myResourceGroup -n myPrivateCloudName --location eastus --cluster-size 3 --network-block xx.xx.xx.xx/22
```

## <a name="verify-deployment-was-successful"></a>Controleren of de implementatie is voltooid

Ga naar de resource groep die u hebt gemaakt en selecteer uw privécloud wanneer de implementatie is voltooid, wordt het volgende scherm weer gegeven en ziet u de status **geslaagd**.

:::image type="content" source="./media/tutorial-create-private-cloud/validate-deployment.png" alt-text="De geïmplementeerde privécloud valideren" border="true":::

## <a name="delete-a-private-cloud"></a>Een privécloud verwijderen

Als u een privécloud hebt die u hebt gecontroleerd en u deze niet meer nodig hebt, kunt u deze verwijderen. Wanneer u een privécloud verwijdert, worden alle clusters samen met al hun onderdelen verwijderd.

Als u dit wilt doen, gaat u naar uw privécloud in het Azure Portal en selecteert u **verwijderen**. Bevestig op de pagina Bevestiging de naam van de privécloud en selecteer **Ja**.

> [!CAUTION]
> Het verwijderen van de privécloud is een onomkeerbaare bewerking. Zodra de privécloud is verwijderd, kunnen de gegevens niet worden hersteld, omdat alle actieve werk belastingen, onderdelen en alle persoonlijke Cloud gegevens en configuratie-instellingen met inbegrip van open bare IP-adressen worden vernietigd. 

## <a name="next-steps"></a>Volgende stappen

In deze zelfstudie hebt u het volgende geleerd:

> [!div class="checklist"]
> * Een privécloud maken
> * De geïmplementeerde Privécloud is geverifieerd

Ga door naar de volgende zelf studie voor meer informatie over het maken van een virtueel netwerk voor gebruik met uw privécloud als onderdeel van het instellen van lokaal beheer voor uw particuliere cloud clusters.

> [!div class="nextstepaction"]
> [Een Virtual Network maken](tutorial-configure-networking.md)
