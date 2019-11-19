---
title: 'Zelf studie: een virtuele Linux-machine maken met een beheerde identiteit van de Azure Marketplace-installatie kopie met behulp van terraform'
description: Een terraform Linux-VM maken met een beheerde identiteits-en extern status beheer met een Azure Marketplace-installatie kopie
ms.topic: tutorial
ms.date: 11/07/2019
ms.openlocfilehash: 6bc6bec2a13dcd5747823de739d1dd11c6027091
ms.sourcegitcommit: 28688c6ec606ddb7ae97f4d0ac0ec8e0cd622889
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/18/2019
ms.locfileid: "74158971"
---
# <a name="tutorial-create-a-linux-vm-with-a-managed-identity-from-the-azure-marketplace-image-using-terraform"></a>Zelf studie: een virtuele Linux-machine maken met een beheerde identiteit vanuit de Azure Marketplace-installatie kopie met behulp van terraform

In dit artikel leest u hoe u een [Terraform Marketplace-installatiekopie](https://azuremarketplace.microsoft.com/marketplace/apps/azure-oss.terraform?tab=Overview) gebruikt om een Ubuntu Linux VM (16.04 LTS) te maken met de nieuwste [Terraform](https://www.terraform.io/intro/index.html)-versie geïnstalleerd en geconfigureerd met behulp van [beheerde identiteiten voor Azure-resources](/azure/active-directory/managed-service-identity/overview). Met deze installatiekopie wordt tevens een externe back-end geconfigureerd om beheer van de [remote state](https://www.terraform.io/docs/state/remote.html) met behulp van Terraform in te schakelen. 

Met de Terraform Marketplace-installatiekopie kunt u eenvoudig Terraform in Azure gaan gebruiken, zonder Terraform handmatig te hoeven installeren en te configureren. 

Voor deze Terraform VM-installatiekopie worden geen softwarekosten in rekening gebracht. U betaalt alleen de gebruiks kosten voor Azure-hardware op basis van de grootte van de ingerichte VM. 

Zie de [pagina met prijzen voor Linux VM](https://azure.microsoft.com/pricing/details/virtual-machines/linux/)voor meer informatie over de reken kosten.

## <a name="prerequisites"></a>Vereisten
Voordat u een Linux terraform-VM kunt maken, moet u een Azure-abonnement hebben. Raadpleeg [Maak vandaag nog uw gratis Azure-account](https://azure.microsoft.com/free/) als u nog geen abonnement hebt.  

## <a name="create-your-terraform-vm"></a>Uw terraform-VM maken 

Hier volgen de stappen voor het maken van een exemplaar van een Linux terraform-VM: 

1. Ga in Azure Portal naar de lijst [Resource maken](https://ms.portal.azure.com/#create/hub).

1. Ga naar de zoekbalk **Zoeken in Marketplace** en zoek **Terraform**. 

1. Selecteer de **maken**. 

1. De volgende secties bevatten invoer voor elk van de stappen in de wizard om de terraform Linux-VM te maken. In het volgende gedeelte staat de informatie die u nodig hebt om alle stappen te configureren.

## <a name="details-on-the-create-terraform-tab"></a>Details op het tabblad Terraform maken

Voer de volgende details in op het tabblad **Terraform maken**:

1. **Basisinstellingen**
    
   * **Naam**: de naam van uw TERRAFORM-VM.
   * **Gebruikersnaam**: de aanmeld-id van de eerste account.
   * **Wachtwoord**: het wachtwoord van de eerste account. (U kunt een openbare SSH-sleutel gebruiken in plaats van een wachtwoord.)
   * **Abonnement**: het abonnement waarop de machine moet worden gemaakt en gefactureerd. U hebt privileges voor het maken van resources nodig voor dit abonnement.
   * **Resourcegroep**: een nieuwe of bestaande resourcegroep.
   * **Locatie**: het meest geschikte datacenter. Doorgaans is dit het datacenter waarop de meeste van uw gegevens zijn opgeslagen of het datacenter dat fysiek het dichtst bij u in de buurt staat voor de snelste netwerktoegang.

2. **Aanvullende instellingen**

   * **Grootte**: grootte van de virtuele machine. 
   * **VM-schijftype**: SSD of HDD.

3. **Samenvatting Terraform**

   * Controleer of alle informatie die u hebt ingevoerd, correct is. 

4. **Kopen**

   * Selecteer **Kopen** om het inrichtingsproces te starten. Er wordt een koppeling gegeven naar de voorwaarden van de transactie. Er zijn geen extra kosten in rekening gebracht voor de virtuele machine die u in de stap grootte hebt gekozen voor de server grootte.

De terraform VM-installatie kopie voert de volgende stappen uit:

* Maakt een virtuele machine met een door het systeem toegewezen identiteit op basis van de Ubuntu 16.04 LTS-installatiekopie.
* Hiermee worden de beheerde identiteiten voor de Azure-resources-extensie op de VM geïnstalleerd zodat OAuth-tokens voor Azure-resources kunnen worden uitgegeven.
* Wijst RBAC-machtigingen toe aan de beheerde identiteit, waardoor de eigenaar rechten krijgt voor de resourcegroep.
* Maakt een map met Terraform-sjablonen (tfTemplate).
* Configureert vooraf een externe status van Terraform met de Azure-back-end.

## <a name="access-and-configure-a-linux-terraform-vm"></a>Een Linux terraform-VM openen en configureren

Nadat u de virtuele machine hebt gemaakt, voert u de volgende stappen uit:

1. Meld u aan bij de VM met SSH. Gebruik de account referenties die u hebt gemaakt in de vorige sectie. In Windows kunt u een SSH-clienthulpprogramma zoals [Putty](https://www.putty.org/) downloaden.

1. Verleen Inzender machtigingen voor het hele abonnement op beheerde identiteiten voor Azure-resources op de virtuele machine. 

    De machtiging Inzender helpt beheerde identiteiten te beheren voor Azure-resources op VM om terraform te gebruiken voor het maken van resources buiten de resource groep van de virtuele machine. Voer deze actie uit door het volgende script uit te voeren: 
    
    ```bash
    . ~/tfEnv.sh
    ```

    Dit script maakt gebruik van het [interactieve aanmeldings mechanisme van Azure cli](/cli/azure/authenticate-azure-cli?view=azure-cli-latest#sign-in-interactively) om te verifiëren met Azure. Dit proces wijst de [machtiging Managed id contributor](/azure/role-based-access-control/built-in-roles#managed-identity-contributor) toe voor het hele abonnement. 

1. De virtuele machine heeft een Terraform-back-end met externe status. Als u deze functie wilt inschakelen in uw terraform-implementatie, moet u het `remoteState.tf` bestand kopiëren naar de hoofdmap van de terraform-scripts.

    ```bash
    cp  ~/tfTemplate/remoteState.tf .
    ```

    Zie [terraform Remote State](https://www.terraform.io/docs/state/remote.html)(Engelstalig) voor meer informatie over extern status beheer. De toegangs sleutel voor opslag wordt weer gegeven in dit bestand. Sluit deze voordat u terraform-configuratie bestanden gaat vastleggen in broncode beheer.

## <a name="next-steps"></a>Volgende stappen

> [!div class="nextstepaction"] 
> [Meer informatie over het gebruik van terraform in azure](/azure/terraform)