---
title: Snelstart - Een virtuele machineschaalset maken in de Azure-portal
description: Ga aan de slag met uw implementaties door te leren hoe u snel een virtuele machine maken om de Azure-portal te schalen.
author: cynthn
tags: azure-resource-manager
ms.service: virtual-machine-scale-sets
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm
ms.topic: quickstart
ms.custom: mvc, H1Hack27Feb2017
ms.date: 10/23/2019
ms.author: cynthn
ms.openlocfilehash: 41bfe86f4635a54c56a8313fabefa1f381f4a4ee
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/26/2020
ms.locfileid: "80062743"
---
# <a name="quickstart-create-a-virtual-machine-scale-set-in-the-azure-portal"></a>Snelstartgids: Een schaalset voor virtuele machines maken in Azure Portal

Met een virtuele-machineschaalset kunt u een reeks identieke virtuele machines met automatisch schalen implementeren en beheren. U kunt het aantal VM’s in de schaalset handmatig schalen of regels voor automatisch schalen definiëren op basis van resourcegebruik zoals CPU, vraag naar geheugen, of netwerkverkeer. Een Azure load balancer verdeelt het verkeer vervolgens naar de VM-exemplaren in de schaalset. In deze snelstartgids gaat u in Azure Portal een schaalset voor virtuele machines maken.

Als u geen Azure-abonnement hebt, maakt u een [gratis account](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) voordat u begint.


## <a name="log-in-to-azure"></a>Meld u aan bij Azure.
Meld u aan bij Azure Portal op https://portal.azure.com.

## <a name="create-a-load-balancer"></a>Een load balancer maken

Azure [load balancer](../load-balancer/load-balancer-overview.md) verdeelt binnenkomend verkeer over gezonde virtuele machine-exemplaren. 

Maak eerst een openbare Standaard Load Balancer met behulp van de portal. De naam en het openbare IP-adres die u maakt, worden automatisch geconfigureerd als de front-end van de load balancer.

1. Typ **load balancer**in het zoekvak . Kies **Load balancer**onder **Marketplace** in de zoekresultaten .
1. Voer op het tabblad **Basisbeginselen** van de pagina **Lastenbalansr maken** de volgende gegevens in of selecteer deze:

    | Instelling                 | Waarde   |
    | ---| ---|
    | Abonnement  | Selecteer uw abonnement.    |    
    | Resourcegroep | Selecteer **Nieuw maken** en typ *myVMSSResourceGroup* in het tekstvak.|
    | Name           | *myLoadBalancer*         |
    | Regio         | Selecteer **Oost-VS**.       |
    | Type          | Select **Openbaar**.       |
    | SKU           | Selecteer **Standaard**.       |
    | Openbaar IP-adres | Selecteer **Nieuw maken**. |
    | Naam openbaar IP-adres  | *MyPip MyPip*   |
    | Toewijzing| Statisch |

1. Wanneer u klaar bent, selecteert u **Controleren + maken** 
1. Nadat de validatie is doorgegeven, selecteert u **Maken**. 

![Een load balancer maken](./media/virtual-machine-scale-sets-create-portal/load-balancer.png)

## <a name="create-virtual-machine-scale-set"></a>Schaalset voor virtuele machines maken
U kunt een schaalset implementeren met een installatiekopie van Windows Server of Linux, zoals RHEL, CentOS, Ubuntu of SLES.

1. Typ **Schaal ingesteld** in het zoekvak. Selecteer in de resultaten onder **Marketplace** **virtuele machineschaalsets**. De pagina **Een virtuele machineschaalset maken** wordt geopend. 
1. Zorg ervoor dat op het tabblad **Basics** onder **Projectgegevens** het juiste abonnement is geselecteerd, en kies **Nieuwe maken** om een nieuwe resourcegroep te maken. Typ *myVMSSResourceGroup* voor de naam en selecteer **OK** . 
1. Typ *myScaleSet* als de naam voor uw schaalset.
1. Selecteer **in Regio**een gebied dat dicht bij uw gebied ligt.
1. Laat de standaardwaarde van **ScaleSet VM's voor** **Orchestrator**.
1. Selecteer een marketplace-afbeelding voor **Afbeelding**. In dit voorbeeld hebben we gekozen voor *Ubuntu Server 18.04 LTS*.
1. Voer de gewenste gebruikersnaam in en selecteer het verificatietype dat u wilt gebruiken.
   - Een **wachtwoord** moet ten minste 12 tekens lang zijn en aan drie van de vier volgende complexiteitsvereisten voldoen: ten minste één kleine letter, één hoofdletter, één cijfer en één speciaal teken. Zie [Wat zijn de wachtwoordvereisten bij het maken van een virtuele machine?](../virtual-machines/windows/faq.md#what-are-the-username-requirements-when-creating-a-vm) voor meer informatie.
   - Als u een installatiekopie voor een Linux-besturingssysteem selecteert, kunt u ook **Openbare SSH-sleutel** kiezen. Geef alleen de openbare sleutel op, zoals *~/.ssh/id_rsa.pub*. U kunt vanuit de portal de Azure Cloud-Shell gebruiken om [SSH-sleutels te maken en gebruiken](../virtual-machines/linux/mac-create-ssh-keys.md).
   
    ![Een virtuele-machineschaalset maken](./media/virtual-machine-scale-sets-create-portal/quick-create-scaleset.png)

1. Selecteer **Volgende** om de andere pagina's te verplaatsen. 
1. Laat de standaardinstellingen voor de **pagina's Instantie** en **Schijven.**
1. Selecteer op de pagina **Netwerken** onder **Taakverdeling**de optie **Ja** om de schaalsetinstanties achter een load balancer te plaatsen. 
1. Selecteer Azure **load balancer**in **taakverdelingsopties**.
1. Selecteer *myLoadBalancer* die u eerder hebt gemaakt in **Selecteer een load balancer.**
1. Selecteer voor **Een backendpool**selecteren , selecteer **Nieuw maken**, typ *myBackendPool*en selecteer **Vervolgens Maken**.
1. Wanneer u klaar bent, selecteert u **Controleren + maken**. 
1. Nadat de validatie is doorstaan, selecteert **u Maken** om de schaalset te implementeren.


## <a name="clean-up-resources"></a>Resources opschonen
De resourcegroep, de schaalset en alle gerelateerde resources kunt u verwijderen wanneer u deze niet meer nodig hebt. Selecteer hiervoor de brongroep voor de schaalset en selecteer **Verwijderen**.


## <a name="next-steps"></a>Volgende stappen
In deze snelstartgids hebt u via Azure Portal een eenvoudige schaalset voor virtuele machines gemaakt. Als u meer wilt weten over schaalsets, gaat u verder met de zelfstudie voor het maken en beheren van schaalsets voor virtuele Azure-machines.

> [!div class="nextstepaction"]
> [Schaalsets voor virtuele Azure-machines maken en beheren](tutorial-create-and-manage-powershell.md)
