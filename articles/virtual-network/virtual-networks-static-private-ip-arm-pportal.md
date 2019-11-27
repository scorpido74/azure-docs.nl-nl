---
title: Privé IP-adressen voor Vm's configureren-Azure Portal
description: Meer informatie over het configureren van privé-IP-adressen voor virtuele machines met behulp van de Azure Portal.
services: virtual-network
documentationcenter: na
author: KumudD
manager: twooley
editor: tysonn
tags: azure-resource-manager
ms.assetid: 11245645-357d-4358-9a14-dd78e367b494
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/04/2016
ms.author: kumud
ms.openlocfilehash: bd734f171f4e10c4227fbab77485a788f02848b3
ms.sourcegitcommit: 8e31a82c6da2ee8dafa58ea58ca4a7dd3ceb6132
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/19/2019
ms.locfileid: "74196635"
---
# <a name="configure-private-ip-addresses-for-a-virtual-machine-using-the-azure-portal"></a>Privé IP-adressen configureren voor een virtuele machine met behulp van de Azure Portal

> [!div class="op_single_selector"]
> * [Azure Portal](virtual-networks-static-private-ip-arm-pportal.md)
> * [PowerShell](virtual-networks-static-private-ip-arm-ps.md)
> * [Azure CLI](virtual-networks-static-private-ip-arm-cli.md)
> * [Azure Portal (klassiek)](virtual-networks-static-private-ip-classic-pportal.md)
> * [PowerShell (klassiek)](virtual-networks-static-private-ip-classic-ps.md)
> * [Azure CLI (klassiek)](virtual-networks-static-private-ip-classic-cli.md)


[!INCLUDE [virtual-networks-static-private-ip-intro-include](../../includes/virtual-networks-static-private-ip-intro-include.md)]

[!INCLUDE [azure-arm-classic-important-include](../../includes/azure-arm-classic-important-include.md)]

Dit artikel is van toepassing op het Resource Manager-implementatiemodel. U kunt ook [een statisch privé-IP-adres beheren in het klassieke implementatie model](virtual-networks-static-private-ip-classic-pportal.md).

[!INCLUDE [virtual-networks-static-ip-scenario-include](../../includes/virtual-networks-static-ip-scenario-include.md)]

In de volgende voorbeeld stappen wordt verwacht dat er al een eenvoudige omgeving is gemaakt. Als u de stappen wilt uitvoeren zoals ze worden weer gegeven in dit document, bouwt u eerst de test omgeving op die wordt beschreven in [een virtueel netwerk maken](quick-create-portal.md).

## <a name="how-to-create-a-vm-for-testing-static-private-ip-addresses"></a>Een virtuele machine maken voor het testen van statische privé-IP-adressen
Het is niet mogelijk om een statisch privé-IP-adres in te stellen tijdens het maken van een virtuele machine in de Resource Manager-implementatie modus met behulp van de Azure Portal. U moet eerst de virtuele machine maken en vervolgens het bijbehorende privé-IP-adres instellen op statisch.

Voer de volgende stappen uit om een virtuele machine met de naam *DNS01* in het *frontend* -subnet van een VNet met de naam *TestVNet*te maken:

1. Navigeer in een browser naar https://portal.azure.com en meld u, indien nodig, aan met uw Azure-account.
2. Klik op **een resource maken** > **Compute** > **Windows Server 2012 R2 Data Center**, ziet u dat in de lijst **een implementatie model selecteren** al **Resource Manager**wordt weer gegeven en klik vervolgens op **maken**, zoals in de volgende afbeelding wordt weer gegeven.
   
    ![Een VM maken in Azure Portal](./media/virtual-networks-static-ip-arm-pportal/figure01.png)
3. Voer in het deel venster **basis beginselen** de naam in van de virtuele machine die u wilt maken (*DNS01* in het scenario), het lokale beheerders account en het wacht woord, zoals in de volgende afbeelding wordt weer gegeven.
   
    ![Het deel venster basis principes](./media/virtual-networks-static-ip-arm-pportal/figure02.png)
4. Zorg ervoor dat de geselecteerde locatie *Central VS*is en klik vervolgens op **bestaande selecteren** onder **resource groep**. Klik vervolgens nogmaals op **resource groep** en vervolgens op *TestRG*en vervolgens op **OK**.
   
    ![Het deel venster basis principes](./media/virtual-networks-static-ip-arm-pportal/figure03.png)
5. Selecteer in het deel venster **een grootte kiezen** de optie **a1 Standard**en klik vervolgens op **selecteren**.
   
    ![Een deel venster grootte kiezen](./media/virtual-networks-static-ip-arm-pportal/figure04.png)    
6. Controleer in het deel venster **instellingen** of de eigenschappen zijn ingesteld met de volgende waarden en klik vervolgens op **OK**.
   
    -**Storage-account**: *vnetstorage*
   
   * **Netwerk**: *TestVNet*
   * **Subnet**: front- *End*
     
     ![Een deel venster grootte kiezen](./media/virtual-networks-static-ip-arm-pportal/figure05.png)     
7. Klik in het deel venster **samen vatting** op **OK**. U ziet de volgende tegel die wordt weer gegeven in het dash board.
   
    ![Een VM maken in Azure Portal](./media/virtual-networks-static-ip-arm-pportal/figure06.png)

Het is raadzaam dat u het privé-IP-adres dat is toegewezen aan de virtuele machine van Azure niet statisch toewijst in het besturings systeem van een VM, tenzij dit nodig is, bijvoorbeeld wanneer [u meerdere IP-adressen toewijst aan een Windows-VM](virtual-network-multiple-ip-addresses-portal.md). Als u het privé-IP-adres hand matig instelt in het besturings systeem, moet u ervoor zorgen dat het hetzelfde adres is als het privé-IP-adres dat is toegewezen aan de Azure- [netwerk interface](virtual-network-network-interface-addresses.md#change-ip-address-settings), of u kunt de verbinding met de virtuele machine verliezen. Meer informatie over instellingen voor [privé-IP-adressen](virtual-network-network-interface-addresses.md#private) . U moet het openbare IP-adres toegewezen aan een virtuele machine van Azure binnen het besturingssysteem van de virtuele machine nooit handmatig toewijzen.

## <a name="how-to-retrieve-static-private-ip-address-information-for-a-vm"></a>Het statische privé IP-adresgegevens voor een virtuele machine ophalen
Voer de volgende stappen uit om de gegevens van het statische privé-IP-adres te bekijken voor de virtuele machine die is gemaakt met de bovenstaande stappen.

1. Klik in het Azure Portal op **Bladeren alle** > **virtuele machines** > **DNS01** > **alle instellingen** > **netwerk interfaces** en klik vervolgens op de enige netwerk interface die wordt weer gegeven.
   
    ![VM-tegel implementeren](./media/virtual-networks-static-ip-arm-pportal/figure07.png)
2. Klik in het deel venster **netwerk interface** op **alle instellingen** > **IP-adressen** en Let op de waarden van de **toewijzing** en het **IP-adres** .
   
    ![VM-tegel implementeren](./media/virtual-networks-static-ip-arm-pportal/figure08.png)

## <a name="how-to-add-a-static-private-ip-address-to-an-existing-vm"></a>Een statisch privé-IP-adres toevoegen aan een bestaande virtuele machine
Voer de volgende stappen uit om een statisch privé-IP-adres toe te voegen aan de virtuele machine die is gemaakt met behulp van de bovenstaande stappen:

1. Klik in het deel venster **IP-adressen** hierboven weer gegeven op **statisch** onder **toewijzing**.
2. Typ *192.168.1.101* voor **IP-adres**en klik vervolgens op **Opslaan**.
   
    ![Een VM maken in Azure Portal](./media/virtual-networks-static-ip-arm-pportal/figure09.png)

> [!NOTE]
> Als nadat u op **Opslaan**hebt geklikt, u ziet dat de toewijzing nog steeds op **dynamisch**is ingesteld, betekent dit dat het IP-adres dat u hebt getypt al in gebruik is. Probeer een ander IP-adres.
> 
> 

Het is raadzaam dat u het privé-IP-adres dat is toegewezen aan de virtuele machine van Azure niet statisch toewijst in het besturings systeem van een VM, tenzij dit nodig is, bijvoorbeeld wanneer [u meerdere IP-adressen toewijst aan een Windows-VM](virtual-network-multiple-ip-addresses-portal.md). Als u het privé-IP-adres hand matig instelt in het besturings systeem, moet u ervoor zorgen dat het hetzelfde adres is als het privé-IP-adres dat is toegewezen aan de Azure- [netwerk interface](virtual-network-network-interface-addresses.md#change-ip-address-settings), of u kunt de verbinding met de virtuele machine verliezen. Meer informatie over instellingen voor [privé-IP-adressen](virtual-network-network-interface-addresses.md#private) . U moet het openbare IP-adres toegewezen aan een virtuele machine van Azure binnen het besturingssysteem van de virtuele machine nooit handmatig toewijzen.

## <a name="how-to-remove-a-static-private-ip-address-from-a-vm"></a>Het verwijderen van een statisch privé IP-adres van een virtuele machine
Als u het statische privé-IP-adres uit de hierboven gemaakte virtuele machine wilt verwijderen, voert u de volgende stap uit:

Klik in het deel venster **IP-adressen** dat hierboven wordt weer gegeven op **dynamisch** onder **toewijzing**en klik vervolgens op **Opslaan**.

## <a name="set-ip-addresses-within-the-operating-system"></a>IP-adressen binnen het besturingssysteem instellen

Het is raadzaam dat u het privé-IP-adres dat is toegewezen aan de virtuele machine van Azure niet statisch toewijst in het besturings systeem van een VM, tenzij dit nodig is, bijvoorbeeld wanneer [u meerdere IP-adressen toewijst aan een Windows-VM](virtual-network-multiple-ip-addresses-portal.md). Als u het privé-IP-adres hand matig instelt in het besturings systeem, moet u ervoor zorgen dat het hetzelfde adres is als het privé-IP-adres dat is toegewezen aan de Azure- [netwerk interface](virtual-network-network-interface-addresses.md#change-ip-address-settings), of u kunt de verbinding met de virtuele machine verliezen. Meer informatie over instellingen voor [privé-IP-adressen](virtual-network-network-interface-addresses.md#private) . U moet het openbare IP-adres toegewezen aan een virtuele machine van Azure binnen het besturingssysteem van de virtuele machine nooit handmatig toewijzen.

## <a name="next-steps"></a>Volgende stappen

Meer informatie over het beheren van [IP-adres instellingen](virtual-network-network-interface-addresses.md).

