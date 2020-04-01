---
title: Zelfstudie - Een hub- en spoke-netwerk valideren in Azure met Terraform
description: Zelfstudie om hub- en spoke-netwerktopologie te valideren met alle virtuele netwerken die met elkaar zijn verbonden.
ms.topic: tutorial
ms.date: 10/26/2019
ms.openlocfilehash: 9ba4780c59e5e9da4999573abbc08ecd2738a2cd
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/24/2020
ms.locfileid: "74159206"
---
# <a name="tutorial-validate-a-hub-and-spoke-network-in-azure-using-terraform"></a>Zelfstudie: Een hub- en spoke-netwerk in Azure valideren met Terraform

In dit artikel voert u de terraformbestanden uit die in het vorige artikel in deze serie zijn gemaakt. Het resultaat is een validatie van de connectiviteit tussen de demo virtuele netwerken.

Deze zelfstudie bestaat uit de volgende taken:

> [!div class="checklist"]
> * Gebruik HCL (HashiCorp Language) om de Hub VNet te implementeren in hub-spoke topologie
> * Terraform-plan gebruiken om de te implementeren resources te verifiëren
> * Terraform toepassen gebruiken om de bronnen in Azure te maken
> * De connectiviteit tussen verschillende netwerken verifiëren
> * Gebruik Terraform om alle grondstoffen te vernietigen

## <a name="prerequisites"></a>Vereisten

1. [Maak een hub- en spoke hybride netwerktopologie met Terraform in Azure.](./terraform-hub-spoke-introduction.md)
1. [On-premises virtueel netwerk maken met Terraform in Azure.](./terraform-hub-spoke-on-prem.md)
1. [Maak een virtueel hubnetwerk met Terraform in Azure.](./terraform-hub-spoke-hub-network.md)
1. [Maak een virtueel netwerktoestel met Terraform in Azure.](./terraform-hub-spoke-hub-nva.md)
1. [Maak een gesproken virtuele netwerken met Terraform in Azure.](./terraform-hub-spoke-spoke-network.md)

## <a name="verify-your-configuration"></a>Uw configuratie verifiëren

Controleer na het voltooien van de [vereisten](#prerequisites)of de juiste config-bestanden aanwezig zijn.

1. Blader naar [Azure Portal](https://portal.azure.com).

1. Azure [Cloud Shell openen](/azure/cloud-shell/overview). Als u nog geen omgeving hebt geselecteerd, selecteert u **Bash** als uw omgeving.

    ![Cloud Shell-prompt](./media/terraform-common/azure-portal-cloud-shell-button-min.png)

1. Ga naar de map `clouddrive`.

    ```bash
    cd clouddrive
    ```

1. Maak de nieuwe directory de actieve directory:

    ```bash
    cd hub-spoke
    ```

1. Voer `ls` de opdracht uit `.tf` om te controleren of de config-bestanden die in de vorige zelfstudies zijn gemaakt, worden weergegeven:

    ![Terraform demo config bestanden](./media/terraform-hub-and-spoke-tutorial-series/hub-spoke-config-files.png)

## <a name="deploy-the-resources"></a>De resources implementeren

1. Initialiseer de Terraform provider:
    
    ```bash
    terraform init
    ```
    
    ![Voorbeeldresultaten van opdracht "terraform init"](./media/terraform-hub-and-spoke-tutorial-series/hub-spoke-terraform-init.png)
    
1. Voer `terraform plan` de opdracht uit om het effect van de implementatie vóór uitvoering te zien:

    ```bash
    terraform plan
    ```
    
    ![Voorbeeldresultaten van opdracht 'terraformplan'](./media/terraform-hub-and-spoke-tutorial-series/hub-spoke-terraform-plan.png)

1. Implementeer de oplossing:

    ```bash
    terraform apply
    ```
    
    Voer `yes` dit in wanneer u wordt gevraagd de implementatie te bevestigen.

    ![Voorbeeldresultaten van de opdracht 'terraform apply'](./media/terraform-hub-and-spoke-tutorial-series/hub-spoke-terraform-apply.png)
    
## <a name="test-the-hub-vnet-and-spoke-vnets"></a>Test de hub VNet en spaak VNets

In dit gedeelte ziet u hoe u de connectiviteit van de gesimuleerde on-premises omgeving met de hub VNet testen.

1. Blader in de Azure-portal naar de **onprem-vnet-rg-brongroep.**

1. Selecteer op het tabblad **onprem-vnet-rg** de VM met de naam **onprem-vm**.

1. Selecteer **Verbinden**.

1. Kopieer naast de tekst **Login met vm local account**de **ssh-opdracht** naar het klembord.

1. Voer vanaf een `ssh` Linux-prompt verbinding met de gesimuleerde on-premises omgeving. Gebruik het wachtwoord `on-prem.tf` dat is opgegeven in het parameterbestand.

1. Voer `ping` de opdracht uit om de verbinding met de jumpbox-VM in de hub VNet te testen:

   ```bash
   ping 10.0.0.68
   ```

1. Voer `ping` de opdracht uit om de connectiviteit met de jumpbox VM's in elke spaak te testen:

   ```bash
   ping 10.1.0.68
   ping 10.2.0.68
   ```

1. Als u de ssh-sessie op de virtuele `exit` **onprem-vm-machine** wilt afsluiten, voert u enter en druk op &lt;Enter>.

## <a name="troubleshoot-vpn-issues"></a>VPN-problemen oplossen

Zie het artikel, [Problemen met een hybride VPN-verbinding voor](/azure/architecture/reference-architectures/hybrid-networking/troubleshoot-vpn)informatie over het oplossen van VPN-fouten.

## <a name="clean-up-resources"></a>Resources opschonen

Wanneer u niet meer nodig bent, verwijdert u de bronnen die zijn gemaakt in de zelfstudiereeks.

1. Verwijder de middelen die in het plan zijn aangegeven:

    ```bash
    terraform destroy
    ```

    Voer `yes` in wanneer u wordt gevraagd de verwijdering van de resources te bevestigen.

1. Mappen wijzigen in de bovenliggende map:

    ```bash
    cd ..
    ```

1. Verwijder `hub-scope` de map (inclusief alle bestanden):

    ```bash
    rm -r hub-spoke
    ```

## <a name="next-steps"></a>Volgende stappen

> [!div class="nextstepaction"] 
> [Meer informatie over het gebruik van Terraform in Azure](/azure/terraform)