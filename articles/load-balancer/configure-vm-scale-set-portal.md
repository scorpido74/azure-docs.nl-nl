---
title: Virtuele machineschaalset configureren met een bestaande Azure Load Balancer - Azure-portal
description: Meer informatie over het configureren van een virtuele machineschaalset met een bestaande Azure Load Balancer.
author: asudbring
ms.author: allensu
ms.service: load-balancer
ms.topic: article
ms.date: 03/25/2020
ms.openlocfilehash: b2a83b226a4b2ddbbd554783de6a2b5c85c92f0a
ms.sourcegitcommit: 8a9c54c82ab8f922be54fb2fcfd880815f25de77
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "80349729"
---
# <a name="configure-a-virtual-machine-scale-set-with-an-existing-azure-load-balancer-using-the-azure-portal"></a>Een virtuele machineschaalset configureren met een bestaande Azure Load Balancer met behulp van de Azure-portal

In dit artikel leert u hoe u een virtuele machineschaalset configureert met een bestaande Azure Load Balancer. 

## <a name="prerequisites"></a>Vereisten

- Een Azure-abonnement.
- Een bestaande standaard sku load balancer in het abonnement waar de virtuele machine schaal set zal worden ingezet.
- Een Azure Virtual Network voor de virtuele machineschaalset.

## <a name="sign-in-to-the-azure-portal"></a>Aanmelden bij Azure Portal

Meld u aan bij [https://portal.azure.com](https://portal.azure.com)de Azure-portal op .



## <a name="deploy-virtual-machine-scale-set-with-existing-load-balancer"></a>Virtuele machineschaalset implementeren met bestaande load balancer

In deze sectie maakt u een virtuele machineschaalset in de Azure-portal met een bestaande Azure-load balancer.

> [!NOTE]
> In de volgende stappen wordt ervan uitgegaan dat een virtueel netwerk met de naam **myVNet** en een Azure load balancer met de naam **myLoadBalancer** eerder is geïmplementeerd.

1. Klik linksboven in het scherm op **Een resource** > **Compute** > **Virtual-machineschaalset** maken of zoek naar virtuele **machineschaal die** is ingesteld in de marktplaatszoekopdracht.

2. Selecteer **Maken**.

3. Voer deze informatie in of selecteer deze informatie in De schaalset voor **virtuele machines**in Het tabblad **Basisbeginselen:**

    | Instelling                        | Waarde                                                                                                 |
    |--------------------------------|-------------------------------------------------------------------------------------------------------|
    | **Projectdetails**            |                                                                                                       |
    | Abonnement                   | selecteer uw Azure-abonnement                                                                        |
    | Resourcegroep                 | Selecteer Nieuw maken, typ **myResourceGroup**en selecteer OK of selecteer een bestaande resourcegroep. |
    | **Details van de schaalset**          |                                                                                                       |
    | Naam van virtuele-machineschaalset | **MijnVMSS invoeren**                                                                                      |
    | Regio                         | Selecteer **Oost-VS 2**                                                                                    |
    | Beschikbaarheidszone              | Selecteer **Geen**                                                                                       |
    | **Instantiedetails**           |                                                                                                       |
    | Installatiekopie                          | Ubuntu **Server 18.04 LTS selecteren**                                                                    |
    | Azure Spot-exemplaar            | Selecteer **Nee**                                                                                         |
    | Grootte                           | Standaard laten staan                                                                                      |
    | **Beheerdersaccount**      |                                                                                                       |
    | Verificatietype            | **Wachtwoord selecteren**                                                                                   |
    | Gebruikersnaam                       | Voer de gebruikersnaam van uw beheerder in        |
    | Wachtwoord                       | Voer uw beheerderswachtwoord in    |
    | Wachtwoord bevestigen               | Voer uw beheerderswachtwoord opnieuw in |


    :::image type="content" source="./media/vm-scale-sets/create-vm-scale-set-01.png" alt-text="Maak een virtuele machineschaalset." border="true":::

4. Selecteer het tabblad **Netwerken.**

5. Voer deze gegevens in of selecteer deze op het tabblad **Netwerken:**

     Instelling                           | Waarde                                                    |
    |-----------------------------------|----------------------------------------------------------|
    | **Virtuele netwerkconfiguratie** |                                                          |
    | Virtueel netwerk                   | Selecteer **myVNet** of uw bestaande virtuele netwerk.      |
    | **Taakverdeling**                |                                                          |
    | Een load balancer gebruiken               | Selecteer **Ja**                                           |
    | **Instellingen voor taakverdeling**       |                                                          |
    | Opties voor taakverdeling            | **Azure load balancer selecteren**                           |
    | Een load balancer selecteren            | **MyLoadBalancer** of uw bestaande load balancer selecteren |
    | Een backendpool selecteren             | Selecteer **myBackendPool** of uw bestaande backendpool.  |

    :::image type="content" source="./media/vm-scale-sets/create-vm-scale-set-02.png" alt-text="Maak een virtuele machineschaalset." border="true":::

6. Selecteer het tabblad **Beheer.**

7. Stel op het tabblad **Beheer** **de diagnose opstarten** in op **Uit**.

8. Selecteer de blauwe **knop Controleren + maken.**

9. Controleer de instellingen en selecteer de knop **Maken.**

## <a name="next-steps"></a>Volgende stappen

In dit artikel hebt u een virtuele machineschaalset geïmplementeerd met een bestaande Azure Load Balancer.  Zie voor meer informatie over virtuele machineschaalsets en load balancer:

- [Wat is Azure Load Balancer?](load-balancer-overview.md)
- [Wat zijn schaalsets voor virtuele machines?](../virtual-machine-scale-sets/overview.md)
