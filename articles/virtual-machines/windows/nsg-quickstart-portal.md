---
title: Poorten openen voor een VM met de Azure-portal
description: Meer informatie over het openen van een poort / het maken van een eindpunt voor uw Windows-vm met behulp van de Azure Portal
author: cynthn
ms.service: virtual-machines-windows
ms.topic: how-to
ms.workload: infrastructure-services
ms.date: 09/27/2018
ms.author: cynthn
ms.openlocfilehash: 1844236a77b688819832b3fe0bf6736beea4bfae
ms.sourcegitcommit: af1cbaaa4f0faa53f91fbde4d6009ffb7662f7eb
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/22/2020
ms.locfileid: "81865489"
---
# <a name="how-to-open-ports-to-a-virtual-machine-with-the-azure-portal"></a>Poorten openen voor een virtuele machine met de Azure-portal
[!INCLUDE [virtual-machines-common-nsg-quickstart](../../../includes/virtual-machines-common-nsg-quickstart.md)]


## <a name="sign-in-to-azure"></a>Aanmelden bij Azure
Meld u aan bij Azure Portal op https://portal.azure.com.

## <a name="create-a-network-security-group"></a>Een netwerkbeveiligingsgroep maken

1. Zoek naar en selecteer de brongroep voor de VM, kies **Toevoegen,** zoek vervolgens naar en selecteer **netwerkbeveiligingsgroep**.

2. Selecteer **Maken**.

    Het **groepsvenster netwerkbeveiliging maken** wordt geopend.

    ![Een netwerkbeveiligingsgroep maken](./media/nsg-quickstart-portal/create-nsg.png)

2. Voer een naam in voor uw netwerkbeveiligingsgroep. 

3. Selecteer of maak een resourcegroep en selecteer vervolgens een locatie.

4. Selecteer **Maken** om de netwerkbeveiligingsgroep te maken.

## <a name="create-an-inbound-security-rule"></a>Een binnenkomende beveiligingsregel maken

1. Selecteer uw nieuwe netwerkbeveiligingsgroep. 

2. Selecteer **Binnenkomende beveiligingsregels**en selecteer **Toevoegen**.

    ![Inkomende regel toevoegen](./media/nsg-quickstart-portal/add-inbound-rule.png)

3. Selecteer **Geavanceerd**. 

4. Kies een algemene **service** in het vervolgkeuzemenu, zoals **HTTP**. U ook **Aangepast** selecteren als u een specifieke poort wilt opgeven om te gebruiken. 

5. Wijzig **destijds** de prioriteit of **naam**. De prioriteit is van invloed op de volgorde waarin regels worden toegepast: hoe lager de numerieke waarde, hoe eerder de regel wordt toegepast.

6. Selecteer **Toevoegen** om de regel te maken.

## <a name="associate-your-network-security-group-with-a-subnet"></a>Uw netwerkbeveiligingsgroep koppelen aan een subnet

De laatste stap bestaat uit het koppelen van uw netwerkbeveiligingsgroep aan een subnet of een specifieke netwerkinterface. In dit voorbeeld koppelen we de netwerkbeveiligingsgroep aan een subnet. 

1. Selecteer **Subnetten**en selecteer **Vervolgens Associate**.

    ![Een netwerkbeveiligingsgroep koppelen aan een subnet](./media/nsg-quickstart-portal/associate-subnet.png)

2. Selecteer het virtuele netwerk en selecteer vervolgens het juiste subnet.

    ![Een netwerkbeveiligingsgroep koppelen aan virtuele netwerken](./media/nsg-quickstart-portal/select-vnet-subnet.png)

    Alle VM's die u met dat subnet verbindt, zijn nu bereikbaar op poort 80.

## <a name="additional-information"></a>Aanvullende informatie

U de stappen in dit artikel ook [uitvoeren met Azure PowerShell](nsg-quickstart-powershell.md).

Met de opdrachten die in dit artikel worden beschreven, u snel het verkeer naar uw VM laten stromen. Netwerkbeveiligingsgroepen bieden veel geweldige functies en granulariteit voor het beheren van de toegang tot uw bronnen. Zie [Netwerkverkeer filteren met een netwerkbeveiligingsgroep](../../virtual-network/tutorial-filter-network-traffic.md)voor meer informatie.

Voor zeer beschikbare webtoepassingen u overwegen uw VM's achter een Azure-loadbalancer te plaatsen. De load balancer distribueert verkeer naar VM's, met een netwerkbeveiligingsgroep die verkeersfiltering biedt. Zie [Virtuele machines voor laadbalans windows in Azure voor](tutorial-load-balancer.md)meer informatie om een zeer beschikbare toepassing te maken.

## <a name="next-steps"></a>Volgende stappen
In dit artikel hebt u een netwerkbeveiligingsgroep gemaakt, een inkomende regel gemaakt waarmee HTTP-verkeer op poort 80 wordt toegestaan en die regel vervolgens gekoppeld aan een subnet. 

In de volgende artikelen vindt u informatie over het maken van meer gedetailleerde omgevingen:
- [Overzicht van Azure Resource Manager](../../azure-resource-manager/management/overview.md)
- [Beveiligingsgroepen](../../virtual-network/security-overview.md)