---
title: Virtuele-machine schaal sets configureren met een bestaande Azure Load Balancer-Azure Portal
description: Meer informatie over het configureren van een schaalset voor virtuele machines met een bestaande Azure Load Balancer.
author: asudbring
ms.author: allensu
ms.service: load-balancer
ms.topic: article
ms.date: 03/25/2020
ms.openlocfilehash: b2a83b226a4b2ddbbd554783de6a2b5c85c92f0a
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/28/2020
ms.locfileid: "80349729"
---
# <a name="configure-a-virtual-machine-scale-set-with-an-existing-azure-load-balancer-using-the-azure-portal"></a>Een schaalset voor virtuele machines configureren met een bestaande Azure Load Balancer met behulp van de Azure Portal

In dit artikel leert u hoe u een schaalset voor virtuele machines kunt configureren met een bestaande Azure Load Balancer. 

## <a name="prerequisites"></a>Vereisten

- Een Azure-abonnement.
- Een bestaande standaard SKU load balancer in het abonnement waarin de schaalset van de virtuele machine wordt geïmplementeerd.
- Een Azure-Virtual Network voor de schaalset van de virtuele machine.

## <a name="sign-in-to-the-azure-portal"></a>Aanmelden bij Azure Portal

Meld u aan bij de Azure Portal op [https://portal.azure.com](https://portal.azure.com).



## <a name="deploy-virtual-machine-scale-set-with-existing-load-balancer"></a>Schaalset voor virtuele machines implementeren met bestaande load balancer

In deze sectie maakt u een schaalset voor virtuele machines in de Azure Portal met een bestaande Azure load balancer.

> [!NOTE]
> Bij de volgende stappen wordt ervan uitgegaan dat een virtueel netwerk met de naam **myVNet** en een Azure-Load Balancer met de naam **myLoadBalancer** eerder is geïmplementeerd.

1. Klik in de linkerbovenhoek van het scherm op **een resource** > **Compute** > maken**virtuele-machine schaalset** of zoek naar **schaalset voor virtuele machines** in de zoek functie voor Marketplace.

2. Selecteer **Maken**.

3. Voer in **een schaalset voor virtuele machines maken**in of Selecteer deze informatie op het tabblad **basis beginselen** :

    | Instelling                        | Waarde                                                                                                 |
    |--------------------------------|-------------------------------------------------------------------------------------------------------|
    | **Project Details**            |                                                                                                       |
    | Abonnement                   | selecteer uw Azure-abonnement                                                                        |
    | Resourcegroep                 | Selecteer Nieuw maken, Voer **myResourceGroup**in en selecteer OK of selecteer een bestaande resource groep. |
    | **Details van schaal sets**          |                                                                                                       |
    | Naam van virtuele-machineschaalset | **MyVMSS** invoeren                                                                                      |
    | Regio                         | Selecteer **VS Oost 2**                                                                                    |
    | Beschikbaarheidszone              | **Geen** selecteren                                                                                       |
    | **Exemplaar Details**           |                                                                                                       |
    | Installatiekopie                          | Selecteer **Ubuntu Server 18,04 LTS**                                                                    |
    | Azure spot-instantie            | **Nee** selecteren                                                                                         |
    | Grootte                           | Standaard behouden                                                                                      |
    | **Administrator-account**      |                                                                                                       |
    | Verificatietype            | **Wacht woord** selecteren                                                                                   |
    | Gebruikersnaam                       | Voer uw gebruikers naam voor de beheerder in        |
    | Wachtwoord                       | Voer uw beheerders wachtwoord in    |
    | Wachtwoord bevestigen               | Voer uw beheerders wachtwoord opnieuw in |


    :::image type="content" source="./media/vm-scale-sets/create-vm-scale-set-01.png" alt-text="Een schaalset voor virtuele machines maken." border="true":::

4. Selecteer het tabblad **netwerken** .

5. Typ of Selecteer deze informatie op het tabblad **netwerk** :

     Instelling                           | Waarde                                                    |
    |-----------------------------------|----------------------------------------------------------|
    | **Virtual Network configuratie** |                                                          |
    | Virtueel netwerk                   | Selecteer **myVNet** of uw bestaande virtuele netwerk.      |
    | **Taakverdeling**                |                                                          |
    | Een load balancer gebruiken               | **Ja** selecteren                                           |
    | **Instellingen voor taak verdeling**       |                                                          |
    | Opties voor taak verdeling            | **Azure-Load Balancer** selecteren                           |
    | Een load balancer selecteren            | **MyLoadBalancer** of uw bestaande Load Balancer selecteren |
    | Een back-end-pool selecteren             | Selecteer **myBackendPool** of uw bestaande back-end-pool.  |

    :::image type="content" source="./media/vm-scale-sets/create-vm-scale-set-02.png" alt-text="Een schaalset voor virtuele machines maken." border="true":::

6. Selecteer het tabblad **beheer** .

7. Stel op het tabblad **beheer** de **Diagnostische gegevens over opstarten** in op **uit**.

8. Selecteer de knop blauwe **beoordeling + maken** .

9. Controleer de instellingen en selecteer de knop **maken** .

## <a name="next-steps"></a>Volgende stappen

In dit artikel hebt u een schaalset voor virtuele machines geïmplementeerd met een bestaande Azure Load Balancer.  Zie voor meer informatie over virtuele-machine schaal sets en load balancer:

- [Wat is Azure Load Balancer?](load-balancer-overview.md)
- [Wat zijn schaalsets voor virtuele machines?](../virtual-machine-scale-sets/overview.md)
