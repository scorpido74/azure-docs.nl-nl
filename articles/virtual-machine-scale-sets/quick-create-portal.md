---
title: 'Snelstartgids: een schaalset voor virtuele machines maken in de Azure Portal'
description: Ga aan de slag met uw implementaties door te leren hoe u snel een virtuele machine kunt maken met een schaal Azure Portal.
author: cynthn
tags: azure-resource-manager
ms.service: virtual-machine-scale-sets
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm
ms.topic: quickstart
ms.custom: H1Hack27Feb2017
ms.date: 10/23/2019
ms.author: cynthn
ms.openlocfilehash: 2acde4efa1dc118498f3c7da29c75e48c0478ac0
ms.sourcegitcommit: 87781a4207c25c4831421c7309c03fce5fb5793f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/23/2020
ms.locfileid: "76543134"
---
# <a name="quickstart-create-a-virtual-machine-scale-set-in-the-azure-portal"></a>Snelstartgids: Een schaalset voor virtuele machines maken in Azure Portal

Met een virtuele-machineschaalset kunt u een reeks identieke virtuele machines met automatisch schalen implementeren en beheren. U kunt het aantal VM’s in de schaalset handmatig schalen of regels voor automatisch schalen definiëren op basis van resourcegebruik zoals CPU, vraag naar geheugen, of netwerkverkeer. Een Azure load balancer verdeelt het verkeer vervolgens naar de VM-exemplaren in de schaalset. In deze snelstartgids gaat u in Azure Portal een schaalset voor virtuele machines maken.

Als u nog geen abonnement op Azure hebt, maak dan een [gratis account](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) aan voordat u begint.


## <a name="log-in-to-azure"></a>Meld u aan bij Azure.
Meld u aan bij Azure Portal op https://portal.azure.com.

## <a name="create-a-load-balancer"></a>Een load balancer maken

Azure [Load Balancer](../load-balancer/load-balancer-overview.md) distribueert binnenkomend verkeer onder gezonde instanties van virtuele machines. 

Maak eerst een open bare Standard Load Balancer met behulp van de portal. De naam en het openbare IP-adres die u maakt, worden automatisch geconfigureerd als de front-end van de load balancer.

1. Typ **Load Balancer**in het zoekvak. Kies **Load Balancer**onder **Marketplace** in de zoek resultaten.
1. Typ of selecteer de volgende informatie op het tabblad **basis principes** van de pagina **Load Balancer maken** :

    | Instelling                 | Waarde   |
    | ---| ---|
    | Abonnement  | Selecteer uw abonnement.    |    
    | Resourcegroep | Selecteer **nieuwe maken** en typ *myVMSSResourceGroup* in het tekstvak.|
    | Name           | *myLoadBalancer*         |
    | Regio         | Selecteer **US - oost**.       |
    | Type          | Selecteer **Openbaar**.       |
    | SKU           | Selecteer **standaard**.       |
    | Openbaar IP-adres | Selecteer **Nieuw maken**. |
    | Naam openbare IP-adres  | *MyPip*   |
    | Toewijzing| Statisch |

1. Wanneer u klaar bent, selecteert u **controleren + maken** 
1. Nadat de validatie is geslaagd, selecteert u **maken**. 

![Een load balancer maken](./media/virtual-machine-scale-sets-create-portal/load-balancer.png)

## <a name="create-virtual-machine-scale-set"></a>Schaalset voor virtuele machines maken
U kunt een schaalset implementeren met een installatiekopie van Windows Server of Linux, zoals RHEL, CentOS, Ubuntu of SLES.

1. Typ **schaal instellen** in het zoekvak. Selecteer in de resultaten onder **Marketplace**de optie **schaal sets voor virtuele machines**. De pagina **schaal sets voor virtuele machines maken** wordt geopend. 
1. Zorg ervoor dat op het tabblad **Basics** onder **Projectgegevens** het juiste abonnement is geselecteerd, en kies **Nieuwe maken** om een nieuwe resourcegroep te maken. Typ *myVMSSResourceGroup* voor de naam en selecteer vervolgens **OK** . 
1. Typ *myScaleSet* als de naam voor de schaalset.
1. Selecteer in **regio**een regio die zich dicht bij u bevindt.
1. Wijzig de standaard waarde van de **schaalset vm's** voor **Orchestrator**.
1. Selecteer een Marketplace-installatie kopie voor de **installatie kopie**. In dit voor beeld hebben we gekozen voor *Ubuntu Server 18,04 LTS*.
1. Voer de gewenste gebruikersnaam in en selecteer het verificatietype dat u wilt gebruiken.
   - Een **wachtwoord** moet ten minste 12 tekens lang zijn en aan drie van de vier volgende complexiteitsvereisten voldoen: ten minste één kleine letter, één hoofdletter, één cijfer en één speciaal teken. Zie [Wat zijn de wachtwoordvereisten bij het maken van een virtuele machine?](../virtual-machines/windows/faq.md#what-are-the-username-requirements-when-creating-a-vm) voor meer informatie.
   - Als u een installatiekopie voor een Linux-besturingssysteem selecteert, kunt u ook **Openbare SSH-sleutel** kiezen. Geef alleen de openbare sleutel op, zoals *~/.ssh/id_rsa.pub*. U kunt vanuit de portal de Azure Cloud-Shell gebruiken om [SSH-sleutels te maken en gebruiken](../virtual-machines/linux/mac-create-ssh-keys.md).
   
    ![Een virtuele-machineschaalset maken](./media/virtual-machine-scale-sets-create-portal/quick-create-scaleset.png)

1. Selecteer **volgende** om de andere pagina's te verplaatsen. 
1. Accepteer de standaard waarden voor de pagina's **exemplaar** en **schijven** .
1. Selecteer op de pagina **netwerken** onder **taak verdeling**de optie **Ja** om de instanties van de schaalset achter een Load Balancer in te stellen. 
1. Selecteer **Azure Load Balancer**in **Opties voor taak verdeling**.
1. In **Selecteer een Load Balancer**selecteert u *myLoadBalancer* die u eerder hebt gemaakt.
1. Voor **een back-end-pool selecteren**selecteert u **Nieuw**, typt u *myBackendPool*en selecteert u **maken**.
1. Wanneer u klaar bent, selecteert u **controleren + maken**. 
1. Nadat de validatie is geslaagd, selecteert u **maken** om de schaalset te implementeren.


## <a name="clean-up-resources"></a>Resources opschonen
De resourcegroep, de schaalset en alle gerelateerde resources kunt u verwijderen wanneer u deze niet meer nodig hebt. Als u dit wilt doen, selecteert u de resource groep voor de schaalset en selecteert u vervolgens **verwijderen**.


## <a name="next-steps"></a>Volgende stappen
In deze snelstartgids hebt u via Azure Portal een eenvoudige schaalset voor virtuele machines gemaakt. Als u meer wilt weten over schaalsets, gaat u verder met de zelfstudie voor het maken en beheren van schaalsets voor virtuele Azure-machines.

> [!div class="nextstepaction"]
> [Schaalsets voor virtuele Azure-machines maken en beheren](tutorial-create-and-manage-powershell.md)
