---
title: Privé-IP-adressen configureren voor VM's - Azure-portal
description: Meer informatie over het configureren van privé-IP-adressen voor virtuele machines met behulp van de Azure-portal.
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
ms.date: 02/07/2020
ms.author: kumud
ms.openlocfilehash: b50875105696dc5c556e2a4a9e756078cf995327
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80060597"
---
# <a name="configure-a-private-ip-address-for-a-vm-using-the-azure-portal"></a>Een privé-IP-adres configureren voor een vm met de Azure-portal

[!INCLUDE [virtual-networks-static-private-ip-intro-include](../../includes/virtual-networks-static-private-ip-intro-include.md)]

[!INCLUDE [virtual-networks-static-ip-scenario-include](../../includes/virtual-networks-static-ip-scenario-include.md)]

In de volgende voorbeeldstappen wordt verwacht dat er al een eenvoudige omgeving wordt gemaakt. Als u de stappen wilt uitvoeren zoals ze in dit document worden weergegeven, maakt u eerst [een virtueel netwerk](quick-create-portal.md#create-a-virtual-network). Gebruik echter in stap 3 deze waarden:

| Instelling | Waarde |
| ------- | ----- |
| Name | *TestVNet (TestVNet)* |
| Adresruimte | *192.168.0.0/16* |
| Resourcegroep | **TestRG** (selecteer indien nodig **Nieuw maken** om het te maken) |
| Subnet - Naam | *Frontend* |
| Subnet - adresbereik | *192.168.1.0/24* |

## <a name="create-a-vm-for-testing-static-private-ip-addresses"></a>Een VM maken voor het testen van statische privé-IP-adressen
Wanneer u een VM maakt in de implementatiemodus Resourcebeheer, u geen statisch privé-IP-adres instellen met de Azure-portal. In plaats daarvan maakt u eerst de VM. Vervolgens u instellen dat het privé-IP statisch is.

Voer de volgende stappen uit om een VM met de naam *DNS01* te maken in het *FrontEnd-subnet* van een virtueel netwerk met de naam *TestVNet:*

1. Selecteer in het menu [azure portal](https://portal.azure.com) de optie Een **resource maken**.

    ![Een resource, Azure-portal maken](./media/virtual-networks-static-ip-arm-pportal/create-a-resource.png)
2. Selecteer **Compute** > **Virtual-machine**.

    ![VM, Azure-portal maken](./media/virtual-networks-static-ip-arm-pportal/compute-virtual-machine.png)
3. Geef **in Basisbeginselen**waarden op voor items zoals beschreven in de volgende tabel. Selecteer vervolgens **&nbsp;&nbsp;Volgende: Schijven** en vervolgens **Volgende:&nbsp;&nbsp;Netwerken**.

    | Item | Waarde |
    | --- | --- |
    | **Abonnement** | Uw huidige abonnement |
    | **Resourcegroep** | **TestRG** (selectie uit vervolgkeuzelijst) |
    | **Naam virtuele machine** | *DNS01* |
    | **Regio** | **(VS) Oost-VS** |
    | **Afbeelding** | **Windows Server 2019 Datacenter** |
    | **Grootte** | **VM-grootte** van **B1ls**, **Aanbod** van **standaard** |
    | **Gebruikersnaam** | De gebruikersnaam van uw beheerdersaccount |
    | **Wachtwoord** | Het wachtwoord voor de gebruikersnaam van uw beheerdersaccount |
    | **Wachtwoord bevestigen** | Het wachtwoord opnieuw |

    ![Tabblad Basis, een virtuele machine maken, Azure-portal](./media/virtual-networks-static-ip-arm-pportal/create-a-virtual-machine-basics.png)
4. Geef in **Netwerken**waarden op voor items zoals beschreven in de volgende tabel en selecteer **Volgende**.

    | Item | Waarde |
    | --- | --- |
    | **Virtueel netwerk** | **TestVNet (TestVNet)** |
    | **Subnet** | **Frontend** |

    ![Tabblad Netwerken, Een virtuele machine maken, Azure-portal](./media/virtual-networks-static-ip-arm-pportal/create-a-virtual-machine-networking.png)
5. Kies in **Beheer**onder **Het opslagaccount Diagnostiek** **vnetstorage**. Als dat opslagaccount niet in de lijst wordt weergegeven, selecteert u **Nieuw maken,** geeft u een **naam** van *vnetopslag*op en selecteert **u OK**. Selecteer tot slot **Controleren&nbsp;+&nbsp;maken**.

    ![Tabblad Beheer, Een virtuele machine maken, Azure-portal](./media/virtual-networks-static-ip-arm-pportal/create-a-virtual-machine-management.png)
6. Bekijk in **Review + create**de overzichtsinformatie en selecteer Vervolgens **Maken**.

    ![Tab bekijken + maken, een virtuele machine maken, Azure-portal](./media/virtual-networks-static-ip-arm-pportal/create-a-virtual-machine-review-create.png)

Het volgende bericht wordt weergegeven zodra de vm is gemaakt.

![Implementatievoltooiingsbericht, Een virtuele machine maken, Azure-portal](./media/virtual-networks-static-ip-arm-pportal/deployment-is-complete.png)

## <a name="retrieve-private-ip-address-information-for-a-vm"></a>Privé-IP-adresgegevens voor een virtuele machine ophalen
Ga als volgende over de persoonlijke IP-adresgegevens van uw nieuwe virtuele machine:

1. Ga naar de [Azure-portal](https://portal.azure.com) om uw VM te vinden. Zoeken naar virtuele machines en selecteer **deze**.

    ![Virtuele machines, zoekvak, Azure-portal](./media/virtual-networks-static-ip-arm-pportal/search-box-virtual-machines.png)

2. Selecteer de naam van uw nieuwe VM **(DNS01).**

    ![Lijst met virtuele machines, Azure-portal](./media/virtual-networks-static-ip-arm-pportal/virtual-machine-list.png)

3. Kies **Netwerken**en selecteer de enige netwerkinterface die wordt weergegeven.

    ![Netwerkinterface, netwerken, virtuele machine, Azure-portal](./media/virtual-networks-static-ip-arm-pportal/networking-network-interface.png)

4. Kies **IP-configuraties**en selecteer de IP-configuratie die in de tabel wordt weergegeven.

    ![IP-configuratie, netwerkinterface, netwerken, virtuele machine, Azure-portal](./media/virtual-networks-static-ip-arm-pportal/network-interface-ip-configurations.png)

5. Noteer in **de instellingen voor privé-IP-adres**onder het **virtuele netwerk/subnet TestVNet/FrontEnd** de **waarde toewijzing** (**Dynamisch** of **Statisch**) en het **IP-adres**.

    ![Dynamische of statische toewijzing, oude instellingen voor privé-IP-adres, IP-configuratie, netwerkinterface, netwerken, virtuele machine, Azure-portal](./media/virtual-networks-static-ip-arm-pportal/private-ip-address-settings-old.png)

## <a name="add-a-static-private-ip-address-to-an-existing-vm"></a>Een statisch privé-IP-adres toevoegen aan een bestaande virtuele virtuele machine
Ga als lid van het volgende over een statisch privé-IP-adres naar uw nieuwe virtuele IP-adres:

1. Stel op de IP-configuratiepagina de toewijzing voor uw privé-IP-adres in **op Statisch**.
2. Wijzig uw **privé-IP-adres** in *192.168.1.101*en selecteer **Opslaan**.
   
    ![Dynamische of statische toewijzing, nieuwe instellingen voor privé-IP-adres, IP-configuratie, netwerkinterface, netwerken, virtuele machine, Azure-portal](./media/virtual-networks-static-ip-arm-pportal/private-ip-address-settings-new.png)

> [!NOTE]
> Als u na het selecteren **van Opslaan** merkt dat de toewijzing nog steeds is ingesteld op **Dynamisch,** is het IP-adres dat u hebt getypt al in gebruik. Probeer een ander IP-adres.

## <a name="remove-a-static-private-ip-address-from-a-vm"></a>Een statisch privé-IP-adres verwijderen uit een virtuele machine
Ga als het verwijderen van het statische privé-IP-adres uit uw vm:

Stel op de pagina IP-configuratie de toewijzing voor uw privé-IP-adres in **op Dynamisch**en selecteer **Opslaan**.

## <a name="set-ip-addresses-within-the-operating-system"></a>IP-adressen instellen in het besturingssysteem

Vanuit het besturingssysteem van een vm moet u niet statisch het *privé-IP* toewijzen dat is toegewezen aan de Azure VM. Alleen statische toewijzing van een privé-IP wanneer dat nodig is, zoals bij het [toewijzen van veel IP-adressen aan VM's.](virtual-network-multiple-ip-addresses-portal.md) Als u het privé-IP-adres handmatig instelt in het besturingssysteem, controleert u of het overeenkomt met het privé-IP-adres dat is toegewezen aan de [Azure-netwerkinterface.](virtual-network-network-interface-addresses.md#change-ip-address-settings) Anders u de verbinding met de VM verliezen. Meer informatie over [instellingen voor privé-IP-adres.](virtual-network-network-interface-addresses.md#private)

U moet ook nooit handmatig het *openbare* IP-adres toewijzen dat is toegewezen aan een virtuele Azure-machine binnen het besturingssysteem van de virtuele machine.

## <a name="next-steps"></a>Volgende stappen

Meer informatie over het beheren van [IP-adresinstellingen](virtual-network-network-interface-addresses.md).
