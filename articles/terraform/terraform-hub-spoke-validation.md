---
title: Een hub-en spoke-netwerk valideren met terraform in azure
description: Zelf studie voor het valideren van hub-en spoke-netwerk topologie met alle virtuele netwerken die met elkaar zijn verbonden.
services: terraform
ms.service: azure
keywords: terraform, hub en spoke, netwerken, hybride netwerken, devops, virtuele machine, azure, vnet-peering,
author: VaijanathB
manager: jeconnoc
ms.author: vaangadi
ms.topic: tutorial
ms.date: 09/20/2019
ms.openlocfilehash: e35af0fcf4a8f1f8f0446be44fe5b0bb6eeec693
ms.sourcegitcommit: f2771ec28b7d2d937eef81223980da8ea1a6a531
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 09/20/2019
ms.locfileid: "71169707"
---
# <a name="tutorial-validate-a-hub-and-spoke-network-with-terraform-in-azure"></a>Zelfstudie: Een hub-en spoke-netwerk valideren met terraform in azure

In dit artikel voert u de terraform-bestanden uit die zijn gemaakt in het vorige artikel in deze serie. Het resultaat is een validatie van de connectiviteit tussen de virtuele demo netwerken.

Deze zelfstudie bestaat uit de volgende taken:

> [!div class="checklist"]
> * Gebruik de HCL (HashiCorp Language) voor het implementeren van de hub VNet in hub-spoke-topologie
> * Het terraform-abonnement gebruiken om te controleren welke resources moeten worden geïmplementeerd
> * Gebruik terraform Toep assen om de resources in azure te maken
> * De connectiviteit tussen verschillende netwerken controleren
> * Terraform gebruiken om alle resources te vernietigen

## <a name="prerequisites"></a>Vereisten

1. [Maak een hub-en-spoke hybride netwerk topologie met terraform in azure](./terraform-hub-spoke-introduction.md).
1. [Maak een on-premises virtueel netwerk met terraform in azure](./terraform-hub-spoke-on-prem.md).
1. [Een hub-virtueel netwerk maken met terraform in azure](./terraform-hub-spoke-hub-network.md).
1. [Maak een hub virtueel netwerk apparaat met terraform in azure](./terraform-hub-spoke-hub-nva.md).
1. [Maak een spoke-virtuele netwerk met terraform in azure](./terraform-hub-spoke-spoke-network.md).

## <a name="verify-your-configuration"></a>De configuratie controleren

Controleer na het volt ooien van de [vereisten](#prerequisites)of de juiste configuratie bestanden aanwezig zijn.

1. Blader naar de [Azure-portal](https://portal.azure.com).

1. Open [Azure Cloud Shell](/azure/cloud-shell/overview). Als u nog geen omgeving hebt geselecteerd, selecteert u **Bash** als uw omgeving.

    ![Cloud Shell-prompt](./media/terraform-common/azure-portal-cloud-shell-button-min.png)

1. Ga naar de map `clouddrive`.

    ```bash
    cd clouddrive
    ```

1. Ga naar de nieuwe map:

    ```bash
    cd hub-spoke
    ```

1. Voer de `ls` opdracht uit om te controleren `.tf` of de configuratie bestanden die zijn gemaakt in de vorige zelf studies, worden weer gegeven:

    ![Terraform-demo configuratie bestanden](./media/terraform-hub-and-spoke-tutorial-series/hub-spoke-config-files.png)

## <a name="deploy-the-resources"></a>De resources implementeren

1. Initialiseer de terraform-provider:
    
    ```bash
    terraform init
    ```
    
    ![Voor beeld van de resultaten van de opdracht ' terraform init '](./media/terraform-hub-and-spoke-tutorial-series/hub-spoke-terraform-init.png)
    
1. Voer de `terraform plan` opdracht uit om het effect van de implementatie te bekijken voordat deze wordt uitgevoerd:

    ```bash
    terraform plan
    ```
    
    ![Voorbeeld resultaten van de opdracht ' terraform plan '](./media/terraform-hub-and-spoke-tutorial-series/hub-spoke-terraform-plan.png)

1. De oplossing implementeren:

    ```bash
    terraform apply
    ```
    
    Voer `yes` in wanneer u wordt gevraagd de implementatie te bevestigen.

    ![Voor beeld van de resultaten van de opdracht ' terraform Apply '](./media/terraform-hub-and-spoke-tutorial-series/hub-spoke-terraform-apply.png)
    
## <a name="test-the-hub-vnet-and-spoke-vnets"></a>De hub VNet en spoke VNets testen

In deze sectie wordt uitgelegd hoe u de connectiviteit van de gesimuleerde on-premises omgeving naar het hub-VNet kunt testen.

1. Blader in het Azure Portal naar de resource groep **premises-vnet-RG** .

1. Op het tabblad **premises-vnet-RG** selecteert u de virtuele machine met de naam **premises-VM**.

1. Selecteer **Verbinden**.

1. Kopieer de **SSH** -opdracht naar het klem bord naast de tekst **aanmelding met een lokaal VM-account**.

1. Uitvoeren vanaf een Linux-prompt `ssh` verbinding maken met de gesimuleerde on-premises omgeving. Gebruik het wacht woord dat is `on-prem.tf` opgegeven in het parameter bestand.

1. Voer de `ping` opdracht uit om de verbinding met de JumpBox-vm in het hub-VNet te testen:

   ```bash
   ping 10.0.0.68
   ```

1. Voer de `ping` opdracht uit om de verbinding met de JumpBox vm's in elke spoke te testen:

   ```bash
   ping 10.1.0.68
   ping 10.2.0.68
   ```

1. Als u de SSH-sessie op de virtuele machine met **premises-VM** wilt afsluiten &lt;, voert `exit` u in en drukt u op ENTER >.

## <a name="troubleshoot-vpn-issues"></a>VPN-problemen oplossen

Zie het artikel [problemen met een hybride VPN-verbinding oplossen](/azure/architecture/reference-architectures/hybrid-networking/troubleshoot-vpn)voor meer informatie over het oplossen van VPN-fouten.

## <a name="clean-up-resources"></a>Resources opschonen

Als u deze niet meer nodig hebt, verwijdert u de resources die zijn gemaakt in de reeks zelf studies.

1. Verwijder de resources die zijn gedeclareerd in het plan:

    ```bash
    terraform destroy
    ```

    Voer `yes` in wanneer u wordt gevraagd het verwijderen van de resources te bevestigen.

1. Wijzig de mappen in de bovenliggende map:

    ```bash
    cd ..
    ```

1. Verwijder de `hub-scope` map (inclusief alle bestanden):

    ```bash
    rm -r hub-spoke
    ```

## <a name="next-steps"></a>Volgende stappen

> [!div class="nextstepaction"] 
> [Meer informatie over het gebruik van terraform in azure](/azure/terraform)