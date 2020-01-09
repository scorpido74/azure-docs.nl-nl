---
title: bestand opnemen
description: bestand opnemen
services: virtual-network
author: genlin
ms.service: virtual-network
ms.topic: include
ms.date: 04/13/2018
ms.author: genli
ms.custom: include file
ms.openlocfilehash: 8d13d5d93dba66fa0dce08ffbf569164fb03398d
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/25/2019
ms.locfileid: "75467446"
---
## <a name="how-to-create-a-classic-vnet-in-the-azure-portal"></a>Een klassiek VNet maken in de Azure Portal
Voer de volgende stappen uit om een klassiek VNet te maken op basis van het voor gaande scenario.

1. Navigeer in een browser naar https://portal.azure.com en meld u, indien nodig, aan met uw Azure-account.
2. Klik op **Een resource maken** > **Netwerken** > **Virtueel netwerk**. U ziet dat de lijst **een implementatie model selecteren** al het **klassieke**systeem bevat. 3. Klik op **maken** zoals wordt weer gegeven in de volgende afbeelding.
   
    ![VNet aanmaken in Azure-portal](./media/virtual-networks-create-vnet-classic-pportal-include/vnet-create-pportal-figure1.gif)
4. In het deel venster **virtueel netwerk** typt u de **naam** van het VNet en klikt u op **adres ruimte**. Configureer uw adres ruimte-instellingen voor het VNet en het eerste subnet en klik vervolgens op **OK**. In de volgende afbeelding ziet u de CIDR-blok instellingen voor ons scenario.
   
    ![Deel venster adres ruimte](./media/virtual-networks-create-vnet-classic-pportal-include/vnet-create-pportal-figure2.png)
5. Klik op **resource groep** en selecteer een resource groep waaraan u het vnet wilt toevoegen, of klik op **nieuwe resource groep maken** om het vnet toe te voegen aan een nieuwe resource groep. In de volgende afbeelding ziet u de instellingen van de resource groep voor een nieuwe resource groep met de naam **TestRG**. Zie [Azure Resource Manager Overview](../articles/azure-resource-manager/management/overview.md#resource-groups) (Overzicht van Azure Resource Manager) voor meer informatie over resourcegroepen.
   
    ![Deel venster Resource groep maken](./media/virtual-networks-create-vnet-classic-pportal-include/vnet-create-pportal-figure3.png)
6. Wijzig zo nodig de instellingen van het **Abonnement** en de **Locatie** voor uw VNet. 
7. Als u VNet niet als tegel wilt laten weergeven in het **Startboard** schakelt u **Vastmaken aan Startboard** uit. 
8. Klik op **maken** en noteer de tegel met de naam **virtueel netwerk maken** , zoals wordt weer gegeven in de volgende afbeelding.
   
    ![VNet maken in de portal](./media/virtual-networks-create-vnet-classic-pportal-include/vnet-create-pportal-figure4.png)
9. Wacht tot het VNet is gemaakt en wanneer u de tegel ziet, klikt u hierop om meer subnetten toe te voegen.
   
    ![VNet maken in de portal](./media/virtual-networks-create-vnet-classic-pportal-include/vnet-create-pportal-figure5.png)
10. De **configuratie** voor uw VNet ziet er als volgt uit. 
   
    ![VNet maken in de portal](./media/virtual-networks-create-vnet-classic-pportal-include/vnet-create-pportal-figure6.png)
11. Klik op **subnetten** > **toevoegen**, typ een **naam** en geef een **adres bereik (CIDR-blok)** voor uw subnet op en klik vervolgens op **OK**. In de volgende afbeelding ziet u de instellingen voor ons huidige scenario.
    
    ![VNet aanmaken in Azure-portal](./media/virtual-networks-create-vnet-classic-pportal-include/vnet-create-pportal-figure7.gif)

