---
title: Privé IP-adressen voor virtuele machines (klassiek) configureren-Azure Portal | Microsoft Docs
description: Meer informatie over het configureren van privé-IP-adressen voor virtuele machines (klassiek) met behulp van de Azure Portal.
services: virtual-network
documentationcenter: na
author: genlin
manager: dcscontentpm
editor: tysonn
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
ms.openlocfilehash: 9de9fd2eb7a46719b841a885ac173f8d9c1271c1
ms.sourcegitcommit: ca359c0c2dd7a0229f73ba11a690e3384d198f40
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 09/17/2019
ms.locfileid: "71059114"
---
# <a name="configure-private-ip-addresses-for-a-virtual-machine-classic-using-the-azure-portal"></a>Privé IP-adressen configureren voor een virtuele machine (klassiek) met behulp van de Azure Portal

[!INCLUDE [virtual-networks-static-private-ip-selectors-classic-include](../../includes/virtual-networks-static-private-ip-selectors-classic-include.md)]

[!INCLUDE [virtual-networks-static-private-ip-intro-include](../../includes/virtual-networks-static-private-ip-intro-include.md)]

[!INCLUDE [azure-arm-classic-important-include](../../includes/azure-arm-classic-important-include.md)]

Dit artikel is van toepassing op het klassieke implementatiemodel. U kunt ook [beheren van een statisch privé IP-adres in het Resource Manager-implementatiemodel](virtual-networks-static-private-ip-arm-pportal.md).

[!INCLUDE [virtual-networks-static-ip-scenario-include](../../includes/virtual-networks-static-ip-scenario-include.md)]

De voorbeeld stappen die volgen, verwachten een eenvoudige omgeving die al is gemaakt. Als u de stappen wilt uitvoeren zoals ze worden weer gegeven in dit document, bouwt u eerst de test omgeving op die wordt beschreven in [een vnet maken](virtual-networks-create-vnet-classic-pportal.md).

## <a name="how-to-specify-a-static-private-ip-address-when-creating-a-vm"></a>Een statisch privé IP-adres opgeven bij het maken van een virtuele machine
Als u een virtuele machine met de naam *DNS01* in het *frontend* -subnet van een VNet met de naam *TestVNet* met een statisch privé-IP van *192.168.1.101*wilt maken, voert u de volgende stappen uit:

1. Navigeer in een browser naar https://portal.azure.com en meld u, indien nodig, aan met uw Azure-account.
2. Selecteer **Nieuw** > **Compute**Windows Server 2012 R2 Data Center, Let op dat de lijst een implementatie model selecteren reeds klassiek bevat en selecteer vervolgens maken. > 
   
    ![Een VM maken in Azure Portal](./media/virtual-networks-static-ip-classic-pportal/figure01.png)
3. Voer onder **VM maken**de naam in van de virtuele machine die moet worden gemaakt (*DNS01* in het scenario), het lokale beheerders account en het wacht woord.
   
    ![Een VM maken in Azure Portal](./media/virtual-networks-static-ip-classic-pportal/figure02.png)
4. Selecteer **optioneel configuratie** > **netwerk** > **Virtual Network**en selecteer vervolgens **TestVNet**. Als **TestVNet** niet beschikbaar is, moet u ervoor zorgen dat u de centrale locatie van de *VS* gebruikt en de test omgeving hebt gemaakt die aan het begin van dit artikel is beschreven.
   
    ![Een VM maken in Azure Portal](./media/virtual-networks-static-ip-classic-pportal/figure03.png)
5. Controleer onder **netwerk**of het subnet dat momenteel is geselecteerd, front- *End*is en selecteer vervolgens **IP-adressen**, onder **IP-adres toewijzing** Selecteer **statisch**en voer *192.168.1.101* in als weer gegeven voor het **IP-adres** stappen.
   
    ![Een VM maken in Azure Portal](./media/virtual-networks-static-ip-classic-pportal/figure04.png)    
6. Selecteer **OK** onder **IP-adressen**, selecteer **OK** onder **netwerk**en selecteer **OK** onder **optionele configuratie**.
7. Selecteer bij **virtuele machine maken**de optie **maken**. De onderstaande tegel wordt weer gegeven in het dash board:
   
    ![Een VM maken in Azure Portal](./media/virtual-networks-static-ip-classic-pportal/figure05.png)

## <a name="how-to-retrieve-static-private-ip-address-information-for-a-vm"></a>Het statische privé IP-adresgegevens voor een virtuele machine ophalen
Voer de volgende stappen uit om de gegevens van het statische privé-IP-adres te bekijken voor de virtuele machine die is gemaakt met de bovenstaande stappen.

1. Selecteer in de Azure Portal **Bladeren alle** > **virtuele machines (klassiek)**  > **DNS01** > **alle instellingen** > **IP-adressen** en Let op de IP-adres toewijzing en IP-adres zoals hieronder wordt weer gegeven.
   
    ![Een VM maken in Azure Portal](./media/virtual-networks-static-ip-classic-pportal/figure06.png)

## <a name="how-to-remove-a-static-private-ip-address-from-a-vm"></a>Het verwijderen van een statisch privé IP-adres van een virtuele machine

Onder **IP-adressen**selecteert u **dynamische** , rechts van de **toewijzing van IP-** adressen, selecteert u **Opslaan**en selecteert u vervolgens **Ja**, zoals wordt weer gegeven in de volgende afbeelding:
   
    ![Create VM in Azure portal](./media/virtual-networks-static-ip-classic-pportal/figure07.png)

## <a name="how-to-add-a-static-private-ip-address-to-an-existing-vm"></a>Een statisch privé-IP-adres toevoegen aan een bestaande virtuele machine

1. Onder **IP-adressen**, zoals eerder weer gegeven, selecteert u **statisch** aan de rechter kant van de **toewijzing van IP-adressen**.
2. Typ *192.168.1.101* voor **IP-adres**, selecteer **Opslaan**en selecteer vervolgens **Ja**.

## <a name="set-ip-addresses-within-the-operating-system"></a>IP-adressen binnen het besturingssysteem instellen

Het verdient aanbeveling dat u geen statisch het privé IP-adres toegewezen aan de virtuele machine van Azure binnen het besturingssysteem van een virtuele machine, toewijst tenzij die nodig zijn. Als u het particuliere IP-adres binnen het besturingssysteem handmatig instelt, zorg ervoor dat deze hetzelfde adres als de privé IP-adres toegewezen aan de Azure-VM of verliest u connectiviteit met de virtuele machine. U moet het openbare IP-adres toegewezen aan een virtuele machine van Azure binnen het besturingssysteem van de virtuele machine nooit handmatig toewijzen.

## <a name="next-steps"></a>Volgende stappen
* Meer informatie over [gereserveerde openbare IP-adres](virtual-networks-reserved-public-ip.md) adressen.
* Meer informatie over [instantieniveau openbare IP (ILPIP)](virtual-networks-instance-level-public-ip.md) adressen.
* Raadpleeg de [gereserveerd IP-REST-API's](https://msdn.microsoft.com/library/azure/dn722420.aspx).

