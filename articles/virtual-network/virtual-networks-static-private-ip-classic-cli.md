---
title: Privé IP-adressen configureren voor Vm's (klassiek)-CLI
titlesuffix: Azure Virtual Network
description: Informatie over het configureren van privé IP-adressen voor virtuele machines (klassiek) met behulp van de Azure classic-opdrachtregelinterface (CLI).
services: virtual-network
documentationcenter: na
author: genlin
manager: dcscontentpm
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/15/2016
ms.author: genli
ms.openlocfilehash: 9deaf4b1d80ad4e55e7c971998e8b1f5ea562257
ms.sourcegitcommit: 8e31a82c6da2ee8dafa58ea58ca4a7dd3ceb6132
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/19/2019
ms.locfileid: "74196586"
---
# <a name="configure-private-ip-addresses-for-a-virtual-machine-classic-using-the-azure-classic-cli"></a>Privé IP-adressen voor een virtuele machine (klassiek) met de klassieke Azure-CLI configureren

[!INCLUDE [virtual-networks-static-private-ip-selectors-classic-include](../../includes/virtual-networks-static-private-ip-selectors-classic-include.md)]

[!INCLUDE [virtual-networks-static-private-ip-intro-include](../../includes/virtual-networks-static-private-ip-intro-include.md)]

[!INCLUDE [azure-arm-classic-important-include](../../includes/azure-arm-classic-important-include.md)]

Dit artikel is van toepassing op het klassieke implementatiemodel. U kunt ook [een statisch privé-IP-adres beheren in het Resource Manager-implementatie model](virtual-networks-static-private-ip-arm-cli.md).

Het voorbeeld van dat Azure klassieke CLI-opdrachten die volgen verwacht een eenvoudige omgeving al gemaakt. Als u de opdrachten wilt uitvoeren zoals ze worden weer gegeven in dit document, bouwt u eerst de test omgeving op die wordt beschreven in [een vnet maken](virtual-networks-create-vnet-classic-cli.md).

## <a name="how-to-specify-a-static-private-ip-address-when-creating-a-vm"></a>Een statisch privé IP-adres opgeven bij het maken van een virtuele machine
Voer de volgende stappen uit om een nieuwe virtuele machine met de naam *DNS01* in een nieuwe Cloud service met de naam *TestService* te maken op basis van het bovenstaande scenario:

1. Als u Azure CLI nog nooit hebt gebruikt, raadpleegt u [De Azure CLI installeren en configureren](/cli/azure/install-cli-version-1.0) en volgt u de instructies tot het punt waar u uw Azure-account en -abonnement moet selecteren.
2. Voer de opdracht **Azure service Create** uit om de Cloud service te maken.
   
        azure service create TestService --location uscentral
   
    Verwachte uitvoer:
   
        info:    Executing command service create
        info:    Creating cloud service
        data:    Cloud service name TestService
        info:    service create command OK
3. Voer de **Azure** -opdracht voor het maken van een VM uit om de virtuele machine te maken. U ziet de waarde voor een statisch privé IP-adres. De lijst die na de uitvoer wordt weergegeven, beschrijft de gebruikte parameters.
   
        azure vm create -l centralus -n DNS01 -w TestVNet -S "192.168.1.101" TestService bd507d3a70934695bc2128e3e5a255ba__RightImage-Windows-2012R2-x64-v14.2 adminuser AdminP@ssw0rd
   
    Verwachte uitvoer:
   
        info:    Executing command vm create
        warn:    --vm-size has not been specified. Defaulting to "Small".
        info:    Looking up image bd507d3a70934695bc2128e3e5a255ba__RightImage-Windows-2012R2-x64-v14.2
        info:    Looking up virtual network
        info:    Looking up cloud service
        warn:    --location option will be ignored
        info:    Getting cloud service properties
        info:    Looking up deployment
        info:    Retrieving storage accounts
        info:    Creating VM
        info:    OK
        info:    vm create command OK
   
   * **-l (of --location)** . Azure-regio waar de virtuele machine wordt gemaakt. In ons scenario *centralus*.
   * **-n (of--VM-naam)** . De naam van de virtuele machine wordt gemaakt.
   * **-w (of--virtueel-netwerk-naam)** . Naam van de VNet waar de virtuele machine wordt gemaakt. 
   * **-S (of--static-IP)** . Statische privé IP-adres voor de virtuele machine.
   * **TestService**. De naam van de cloudservice waar de virtuele machine wordt gemaakt.
   * **bd507d3a70934695bc2128e3e5a255ba__RightImage-Windows-2012R2-x64-v-14.2**. De installatiekopie die wordt gebruikt om de VM te maken.
   * **adminuser**. Lokale beheerder voor de Windows-VM.
   * <strong>AdminP@ssw0rd</strong>. Lokale administrator-wachtwoord voor de Windows-VM.

## <a name="how-to-retrieve-static-private-ip-address-information-for-a-vm"></a>Het statische privé IP-adresgegevens voor een virtuele machine ophalen
Als u de gegevens van het statische privé-IP-adres voor de virtuele machine die is gemaakt met het bovenstaande script, wilt weer geven, voert u de volgende Azure CLI-opdracht uit en bekijkt u de waarde voor *netwerk StaticIP*:

    azure vm static-ip show DNS01

Verwachte uitvoer:

    info:    Executing command vm static-ip show
    info:    Getting virtual machines
    data:    Network StaticIP "192.168.1.101"
    info:    vm static-ip show command OK

## <a name="how-to-remove-a-static-private-ip-address-from-a-vm"></a>Het verwijderen van een statisch privé IP-adres van een virtuele machine
Toegevoegd aan de virtuele machine in het bovenstaande script de volgende Azure CLI-opdracht uitvoeren om te verwijderen van het statische privé IP-adres:

    azure vm static-ip remove DNS01

Verwachte uitvoer:

    info:    Executing command vm static-ip remove
    info:    Getting virtual machines
    info:    Reading network configuration
    info:    Updating network configuration
    info:    vm static-ip remove command OK

## <a name="how-to-add-a-static-private-ip-to-an-existing-vm"></a>Een statisch privé IP-adres toevoegen aan een bestaande virtuele machine
Voer de volgende opdracht uit om een statisch privé-IP-adres toe te voegen aan de virtuele machine die is gemaakt met behulp van het bovenstaande script:

    azure vm static-ip set DNS01 192.168.1.101

Verwachte uitvoer:

    info:    Executing command vm static-ip set
    info:    Getting virtual machines
    info:    Looking up virtual network
    info:    Reading network configuration
    info:    Updating network configuration
    info:    vm static-ip set command OK

## <a name="set-ip-addresses-within-the-operating-system"></a>IP-adressen binnen het besturingssysteem instellen

Het verdient aanbeveling dat u geen statisch het privé IP-adres toegewezen aan de virtuele machine van Azure binnen het besturingssysteem van een virtuele machine, toewijst tenzij die nodig zijn. Als u het particuliere IP-adres binnen het besturingssysteem handmatig instelt, zorg ervoor dat deze hetzelfde adres als de privé IP-adres toegewezen aan de Azure-VM of verliest u connectiviteit met de virtuele machine. Wijs het open bare IP-adres dat is toegewezen aan een virtuele machine van Azure niet hand matig toe binnen het besturings systeem van de virtuele machine.

## <a name="next-steps"></a>Volgende stappen
* Meer informatie over [gereserveerde open bare IP-](virtual-networks-reserved-public-ip.md) adressen.
* Meer informatie over [open bare IP-adressen op exemplaar niveau (ILPIP)](virtual-networks-instance-level-public-ip.md) .
* Raadpleeg de [gereserveerd IP rest-api's](https://msdn.microsoft.com/library/azure/dn722420.aspx).