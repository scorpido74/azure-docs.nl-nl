---
title: Zelfstudie - vSphere-cluster implementeren in Azure
description: Meer informatie over het implementeren van een vSphere-cluster in Azure met behulp van Azure VMware Solution (AVS)
ms.topic: tutorial
ms.date: 05/04/2020
ms.openlocfilehash: fc753f43563650357cf43c102e94f0057b62a406
ms.sourcegitcommit: 64fc70f6c145e14d605db0c2a0f407b72401f5eb
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 05/27/2020
ms.locfileid: "83873738"
---
# <a name="tutorial-deploy-an-avs-private-cloud-in-azure"></a>Zelfstudie: Een AVS-privécloud in Azure implementeren

Met de Azure VMware-oplossing (AVS) hebt u de mogelijkheid om een vSphere-cluster in Azure te implementeren. De minimale initiële implementatie is drie hosts. Extra hosts kunnen een voor een worden toegevoegd, maximaal 16 hosts per cluster. 

Omdat AVS u niet in staat stelt om uw privécloud met uw on-premises vCenter te beheren bij het starten, moet u aanvullende configuratie uitvoeren en verbinding maken met een lokaal vCenter-exemplaar, een virtueel netwerk en meer. Deze procedures en gerelateerde vereisten worden behandeld in deze reeks zelfstudies.

In deze zelfstudie leert u het volgende:

> [!div class="checklist"]
> * Een AVS-privécloud maken
> * Controleer of de privécloud is geïmplementeerd

## <a name="prerequisites"></a>Vereisten

- Een Azure-account met een actief abonnement. [Gratis een account maken](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)
- De juiste beheerdersrechten en machtigingen voor het maken van een privécloud.
- Zorg ervoor dat de juiste netwerken zijn geconfigureerd zoals beschreven in [ Zelfstudie: Controlelijst voor netwerken](tutorial-network-checklist.md).

## <a name="register-the-resource-provider"></a>De resourceprovider registreren

Als u de Azure VMware-oplossing wilt gebruiken, moet u eerst de resourceprovider registreren. In het volgende voorbeeld wordt de resourceprovider geregistreerd bij uw abonnement.

```azurecli-interactive
az provider register -n Microsoft.AVS --subscription <your subscription ID>
```

Zie [Azure-resourceproviders en -typen](../azure-resource-manager/management/resource-providers-and-types.md) voor meer manieren om de Azure Synapse-resourceprovider te registeren.

## <a name="sign-in-to-the-azure-portal"></a>Aanmelden bij Azure Portal

Meld u aan bij de [Azure-portal](https://portal.azure.com).

## <a name="create-a-private-cloud"></a>Een privécloud maken

U kunt een privécloud maken met behulp van de [Azure-portal](#azure-portal) of door de [Azure CLI](#azure-cli) te gebruiken.

### <a name="azure-portal"></a>Azure Portal

Selecteer **En een nieuwe resource maken**in de Azure-portal. Typ in het tekstvak **Marketplace doorzoeken** `Azure VMware Solution` en selecteer **Azure VMware Solution** uit de lijst. Selecteer **Maken** in het venster **Azure VMware Solution**

Voer op het tabblad **Basisinstellingen** waarden in voor de velden. De volgende tabel bevat een gedetailleerde lijst met eigenschappen.

| Veld   | Waarde  |
| ---| --- |
| **Abonnement** | Het abonnement dat u wilt gebruiken voor de implementatie.|
| **Resourcegroep** | De resourcegroep voor uw privécloud-resources. |
| **Locatie** | Selecteer een locatie, zoals **VS-Oost**.|
| **Resourcenaam** | De naam van de privécloud van uw AVS. |
| **SKU** | Selecteer de followng SKU-waarde: AV36 |
| **Hosts** | Dit is het aantal hosts dat aan de privécloud moet worden toegevoegd. De standaardwaarde is 3. Deze waarde kan na de implementatie worden verhoogd of verlaagd.  |
| **Beheerderswachtwoord van vCenter** | Voer een wachtwoord voor de cloudbeheerder in. |
| **Manager-wachtwoord van NSX-T** | Voer een NSX-T-wachtwoord voor de cloudbeheerder in. |
| **Adres blokkeren** | Voer een IP-adresblok in voor het CIDR-netwerk voor de privécloud. Een voorbeeld is 10.175.0.0/22. |

:::image type="content" source="./media/tutorial-create-private-cloud/create-private-cloud.png" alt-text="een privécloud maken" border="true":::

Als dit gereed is, selecteert u **Controleren + maken**. Controleer de ingevoerde gegevens in het volgende scherm. Als de gegevens juist zijn, selecteert u **Maken**.

> [!NOTE]
> Deze stap duurt ongeveer twee uur. 

### <a name="azure-cli"></a>Azure CLI

U kunt ook de Azure CLI gebruiken om een AVS-privécloud te maken in Azure. Als u dit wilt doen, kunt u de Azure Cloud Shell gebruiken. In de volgende stappen ziet u hoe u dit doet.

#### <a name="open-azure-cloud-shell"></a>Azure Cloud Shell openen

Azure Cloud Shell is een gratis interactieve shell waarmee u de stappen in dit artikel kunt uitvoeren. In deze shell zijn algemene Azure-hulpprogramma's vooraf geïnstalleerd en geconfigureerd voor gebruik met uw account.

Als u Cloud Shell wilt openen, selecteert u **Proberen** in de rechterbovenhoek van een codeblok. U kunt Cloud Shell ook openen in een afzonderlijk browsertabblad door naar https://shell.azure.com/bash te gaan. Klik op **Kopiëren** om de codeblokken te kopiëren, plak deze in Cloud Shell en druk vervolgens op **Enter** om de code uit te voeren.

#### <a name="create-a-resource-group"></a>Een resourcegroep maken

Een resourcegroep maken met de opdracht [az group create](/cli/azure/group). Een Azure-resourcegroep is een logische container waarin Azure-resources worden geïmplementeerd en beheerd. In het volgende voorbeeld wordt een resourcegroep met de naam *myResourceGroup* gemaakt op de locatie *eastus*:

```azurecli-interactive
az group create --name myResourceGroup --location eastus
```

#### <a name="create-a-private-cloud"></a>Een privécloud maken

Als u een privécloud wilt maken, moet u een naam voor de resourcegroep opgeven, een naam voor de privécloud, een locatie, de grootte van het cluster


|Eigenschap  |Beschrijving  |
|---------|---------|
|Naam resourcegroep     | De naam van de resourcegroep waarnaar u de privécloud implementeert.        |
|Naam van privécloud     | De naam voor de privécloud.        |
|Locatie     | De locatie die wordt gebruikt voor de privécloud         |
|Grootte van cluster     | De grootte van de cluster. De minimumwaarde is 3.         |
|Netwerk blokkeren     | Het CIDR-bereik dat moet worden gebruikt voor de privécloud. Het wordt aanbevolen dat deze uniek is voor uw on-premises omgeving en uw Azure-omgeving.        |

```azurecli-interactive
az vmware private-cloud create -g myResourceGroup -n myPrivateCloudName --location eastus --cluster-size 3 --network-block xx.xx.xx.xx/22
```

## <a name="verify-deployment-was-successful"></a>Controleren of de implementatie is gelukt

Ga naar de resourcegroep die u hebt gemaakt en selecteer uw privécloud wanneer de implementatie is voltooid, wordt het volgende scherm weergegeven en ziet u de status van **Gelukt**.

:::image type="content" source="./media/tutorial-create-private-cloud/validate-deployment.png" alt-text="Controleer of de privécloud is geïmplementeerd" border="true":::

## <a name="delete-a-private-cloud"></a>Een privécloud verwijderen

Als u een AVS-privécloud hebt die u hebt gecontroleerd en u deze niet meer nodig hebt, kunt u deze verwijderen. Wanneer u een privécloud verwijdert, worden alle clusters samen met al hun onderdelen verwijderd.

Als u dit wilt doen, gaat u naar uw privécloud in de Azure-portal en selecteert u **Verwijderen**. Bevestig op de pagina Bevestiging de naam van de privécloud en selecteer **Ja**.

> [!CAUTION]
> Het verwijderen van de privécloud is een onomkeerbare bewerking. Zodra de privécloud is verwijderd, kunnen de gegevens niet worden hersteld, omdat alle actieve workloads, onderdelen en alle persoonlijke cloudgegevens en configuratie-instellingen met inbegrip van openbare IP-adressen worden vernietigd. 

## <a name="next-steps"></a>Volgende stappen

In deze zelfstudie hebt u het volgende geleerd:

> [!div class="checklist"]
> * Een AVS-privécloud maken
> * Gecontroleerd of de privécloud is geïmplementeerd

Ga door naar de volgende zelfstudie voor meer informatie over het maken van een virtueel netwerk voor gebruik met uw privécloud als onderdeel van het instellen van lokaal beheer voor uw particuliere cloudclusters.

> [!div class="nextstepaction"]
> [Een virtueel netwerk maken](tutorial-configure-networking.md)
