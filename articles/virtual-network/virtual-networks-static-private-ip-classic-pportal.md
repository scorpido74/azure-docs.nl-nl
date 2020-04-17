---
title: Privé-IP-adressen configureren voor VM's (Classic) - Azure-portal | Microsoft Documenten
description: Meer informatie over het configureren van privé-IP-adressen voor virtuele machines (Classic) met behulp van de Azure-portal.
services: virtual-network
documentationcenter: na
author: genlin
manager: dcscontentpm
tags: azure-service-management
ms.assetid: b8ef8367-58b2-42df-9f26-3269980950b8
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/04/2016
ms.author: genli
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 0bc080ed41f32ae2af018e9316e67ab38c2d0650
ms.sourcegitcommit: b55d7c87dc645d8e5eb1e8f05f5afa38d7574846
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/16/2020
ms.locfileid: "81449898"
---
# <a name="configure-private-ip-addresses-for-a-virtual-machine-classic-using-the-azure-portal"></a>Privé-IP-adressen configureren voor een virtuele machine (Classic) met behulp van de Azure-portal

[!INCLUDE [virtual-networks-static-private-ip-selectors-classic-include](../../includes/virtual-networks-static-private-ip-selectors-classic-include.md)]

[!INCLUDE [virtual-networks-static-private-ip-intro-include](../../includes/virtual-networks-static-private-ip-intro-include.md)]

[!INCLUDE [azure-arm-classic-important-include](../../includes/azure-arm-classic-important-include.md)]

Dit artikel is van toepassing op het klassieke implementatiemodel. U ook [een statisch privé-IP-adres beheren in het implementatiemodel resourcebeheer](virtual-networks-static-private-ip-arm-pportal.md).

[!INCLUDE [virtual-networks-static-ip-scenario-include](../../includes/virtual-networks-static-ip-scenario-include.md)]

De voorbeeldstappen die volgen, verwachten een eenvoudige omgeving die al is gemaakt. Als u de stappen wilt uitvoeren zoals deze in dit document worden weergegeven, bouwt u eerst de testomgeving die is beschreven in [een vnet maken.](virtual-networks-create-vnet-classic-pportal.md)

## <a name="how-to-specify-a-static-private-ip-address-when-creating-a-vm"></a>Een statisch privé-IP-adres opgeven bij het maken van een vm
Als u een VM met de naam *DNS01* wilt maken in het *FrontEnd-subnet* van een VNet met de naam *TestVNet* met een statisch privé-IP van *192.168.1.101,* voert u de volgende stappen uit:

1. Navigeer in een browser naar https://portal.azure.com en meld u, indien nodig, aan met uw Azure-account.
2. Selecteer **NIEUW** > **Gegevensberekent** > **Windows Server 2012 R2-datacenter**, merk op dat de lijst Een **implementatiemodel selecteren** al **Klassiek**weergeeft en selecteer **Vervolgens Maken**.
   
    ![VM maken in Azure-portal](./media/virtual-networks-static-ip-classic-pportal/figure01.png)
3. Voer **onder VM maken**de naam in van de vm die moet worden gemaakt *(DNS01* in het scenario), het lokale beheerdersaccount en het wachtwoord.
   
    ![VM maken in Azure-portal](./media/virtual-networks-static-ip-classic-pportal/figure02.png)
4. Selecteer Het**virtuele netwerk van**het **configuratienetwerk** > **Network** > optioneel en selecteer **Vervolgens TestVNet**. Als **TestVNet** niet beschikbaar is, controleert u of u de *centrale locatie in* de VS gebruikt en de testomgeving hebt gemaakt die aan het begin van dit artikel is beschreven.
   
    ![VM maken in Azure-portal](./media/virtual-networks-static-ip-classic-pportal/figure03.png)
5. Controleer **onder Netwerk**of het subnet dat momenteel is geselecteerd *FrontEnd*is, selecteer **vervolgens IP-adressen**, selecteer onder **IP-adrestoewijzing** **Selecteer Statisch**en voer vervolgens *192.168.1.101* in voor **IP-adres** zoals hieronder te zien.
   
    ![VM maken in Azure-portal](./media/virtual-networks-static-ip-classic-pportal/figure04.png)    
6. Selecteer **OK** onder **IP-adressen,** selecteer **OK** onder **Netwerk**en selecteer **OK** onder **Optionele config**.
7. Selecteer **onder VM maken**de optie **Maken**. Let op de onderstaande tegel in uw dashboard:
   
    ![VM maken in Azure-portal](./media/virtual-networks-static-ip-classic-pportal/figure05.png)

## <a name="how-to-retrieve-static-private-ip-address-information-for-a-vm"></a>Statische privé-IP-adresgegevens ophalen voor een virtuele-
Voer de onderstaande stappen uit om de statische privé-IP-adresgegevens voor de VM te bekijken die met de bovenstaande stappen zijn gemaakt.

1. Selecteer in de Azure-portal **BROWSE ALL** > **Virtual machines (classic)** > **DNS01** > **Alle instellingen** > **IP-adressen** en zie de IP-adrestoewijzing en IP-adres zoals hieronder te zien.
   
    ![VM maken in Azure-portal](./media/virtual-networks-static-ip-classic-pportal/figure06.png)

## <a name="how-to-remove-a-static-private-ip-address-from-a-vm"></a>Een statisch privé-IP-adres verwijderen uit een virtuele machine

Selecteer onder **IP-adressen**De optie **Dynamisch** rechts van **IP-adrestoewijzing,** selecteer **Opslaan**en selecteer **Vervolgens Ja,** zoals in de volgende afbeelding wordt weergegeven:
   
    ![Create VM in Azure portal](./media/virtual-networks-static-ip-classic-pportal/figure07.png)

## <a name="how-to-add-a-static-private-ip-address-to-an-existing-vm"></a>Een statisch privé-IP-adres toevoegen aan een bestaande virtuele-

1. Selecteer onder **IP-adressen**die eerder worden **weergegeven, Statisch** rechts van **IP-adrestoewijzing**.
2. Typ *192.168.1.101* voor **IP-adres,** selecteer **Opslaan**en selecteer **Vervolgens Ja**.

## <a name="set-ip-addresses-within-the-operating-system"></a>IP-adressen instellen in het besturingssysteem

Het wordt aanbevolen dat u het privé-IP-ip dat is toegewezen aan de virtuele Azure-machine niet statisch toewijst in het besturingssysteem van een vm, tenzij dit nodig is. Als u het privé-IP-adres in het besturingssysteem handmatig instelt, moet u ervoor zorgen dat het hetzelfde adres is als het privé-IP-adres dat is toegewezen aan de Azure VM, of u de verbinding met de virtuele machine verliezen. U mag nooit handmatig het openbare IP-adres toewijzen dat is toegewezen aan een virtuele Azure-machine in het besturingssysteem van de virtuele machine.

## <a name="next-steps"></a>Volgende stappen
* Meer informatie over [gereserveerde openbare IP-adressen.](virtual-networks-reserved-public-ip.md)
* Meer informatie over [IP-adressen op instantieniveau.](virtual-networks-instance-level-public-ip.md)
* Raadpleeg de [gereserveerde IP REST API's](https://msdn.microsoft.com/library/azure/dn722420.aspx).

