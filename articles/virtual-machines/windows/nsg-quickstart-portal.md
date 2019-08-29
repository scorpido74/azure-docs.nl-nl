---
title: Poorten openen voor een virtuele machine met behulp van de Azure Portal | Microsoft Docs
description: Meer informatie over het openen van een poort/het maken van een eind punt naar uw Windows-VM met behulp van het Resource Manager-implementatie model in azure Portal
services: virtual-machines-windows
documentationcenter: ''
author: cynthn
manager: gwallace
editor: ''
ms.assetid: f7cf0319-5ee7-435e-8f94-c484bf5ee6f1
ms.service: virtual-machines-windows
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 09/27/2018
ms.author: cynthn
ms.openlocfilehash: f9edee7a0ff19a536d0ea719ede6d0cd2e9d6ac7
ms.sourcegitcommit: 44e85b95baf7dfb9e92fb38f03c2a1bc31765415
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 08/28/2019
ms.locfileid: "70102634"
---
# <a name="how-to-open-ports-to-a-virtual-machine-with-the-azure-portal"></a>Poorten openen voor een virtuele machine met de Azure Portal
[!INCLUDE [virtual-machines-common-nsg-quickstart](../../../includes/virtual-machines-common-nsg-quickstart.md)]


## <a name="sign-in-to-azure"></a>Aanmelden bij Azure
Meld u aan bij Azure Portal op https://portal.azure.com.

## <a name="create-a-network-security-group"></a>Een netwerkbeveiligingsgroep maken

1. Zoek en selecteer de resource groep voor de virtuele machine, klik op **toevoegen**, zoek naar en selecteer **netwerk beveiligings groep**.

2. Selecteer **Maken**.

    Het venster **netwerk beveiligings groep maken** wordt geopend.

    ![Een netwerkbeveiligingsgroep maken](./media/nsg-quickstart-portal/create-nsg.png)

2. Voer een naam in voor de netwerk beveiligings groep. 

3. Selecteer of maak een resource groep en selecteer vervolgens een locatie.

4. Selecteer **maken** om de netwerk beveiligings groep te maken.

## <a name="create-an-inbound-security-rule"></a>Een regel voor binnenkomende beveiliging maken

1. Selecteer de nieuwe netwerk beveiligings groep. 

2. Selecteer **beveiligings regels voor binnenkomende verbindingen**en selecteer vervolgens **toevoegen**.

    ![Binnenkomende regel toevoegen](./media/nsg-quickstart-portal/add-inbound-rule.png)

3. Selecteer **Geavanceerd**. 

4. Kies een algemene **service** in de vervolg keuzelijst, zoals **http**. U kunt ook **aangepast** selecteren als u een specifieke poort wilt opgeven. 

5. Desgewenst kunt u de **prioriteit** of **naam**wijzigen. De prioriteit is van invloed op de volg orde waarin regels worden toegepast: de lagere numerieke waarde, de eerdere regel wordt toegepast.

6. Selecteer **toevoegen** om de regel te maken.

## <a name="associate-your-network-security-group-with-a-subnet"></a>Uw netwerk beveiligings groep koppelen aan een subnet

De laatste stap bestaat uit het koppelen van uw netwerkbeveiligingsgroep aan een subnet of een specifieke netwerkinterface. In dit voor beeld koppelen we de netwerk beveiligings groep aan een subnet. 

1. Selecteer **subnetten**en selecteer vervolgens **koppelen**.

    ![Een netwerk beveiligings groep koppelen aan een subnet](./media/nsg-quickstart-portal/associate-subnet.png)

2. Selecteer het virtuele netwerk en selecteer vervolgens het juiste subnet.

    ![Een netwerk beveiligings groep koppelen aan een virtueel netwerk](./media/nsg-quickstart-portal/select-vnet-subnet.png)

    Alle Vm's waarmee u verbinding maakt met dat subnet, zijn nu bereikbaar op poort 80.

## <a name="additional-information"></a>Aanvullende informatie

U kunt [de stappen in dit artikel ook uitvoeren](nsg-quickstart-powershell.md)met behulp van Azure PowerShell.

Met de opdrachten die in dit artikel worden beschreven, kunt u snel verkeer ontvangen dat naar uw virtuele machine stroomt. Netwerk beveiligings groepen bieden veel fantastische functies en granulatie voor het beheren van de toegang tot uw resources. Zie [netwerk verkeer filteren met een netwerk beveiligings groep](../../virtual-network/tutorial-filter-network-traffic.md)voor meer informatie.

Voor Maxi maal beschik bare webtoepassingen kunt u overwegen uw Vm's achter een Azure-load balancer te plaatsen. De load balancer distribueert verkeer naar Vm's, met een netwerk beveiligings groep die verkeer filtering biedt. Zie [taak verdeling van virtuele Windows-machines in azure om een Maxi maal beschik bare toepassing te maken](tutorial-load-balancer.md)voor meer informatie.

## <a name="next-steps"></a>Volgende stappen
In dit artikel hebt u een netwerk beveiligings groep gemaakt, een regel voor binnenkomende verbindingen gemaakt waarmee HTTP-verkeer wordt toegestaan op poort 80, en die regel vervolgens aan een subnet is gekoppeld. 

In de volgende artikelen vindt u informatie over het maken van meer gedetailleerde omgevingen:
- [Overzicht van Azure Resource Manager](../../azure-resource-manager/resource-group-overview.md)
- [Beveiligings groepen](../../virtual-network/security-overview.md)