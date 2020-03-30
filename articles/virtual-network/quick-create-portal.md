---
title: 'Een virtueel netwerk maken: quickstart - Azure-portal'
titleSuffix: Azure Virtual Network
description: "Snelstart: maak een virtueel netwerk in de Azure-portal. Met deze netwerken kunnen Azure-bronnen, zoals VM's, veilig met elkaar en het internet communiceren."
author: KumudD
tags: azure-resource-manager
ms.service: virtual-network
ms.devlang: na
ms.topic: quickstart
ms.tgt_pltfrm: virtual-network
ms.workload: infrastructure
ms.date: 03/05/2020
ms.author: kumud
ms.openlocfilehash: 1a1593566b8bdb72f322d64c1ee99c7018f49329
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/26/2020
ms.locfileid: "79240070"
---
# <a name="quickstart-create-a-virtual-network-using-the-azure-portal"></a>Snelstart: een virtueel netwerk maken met Azure Portal

In deze quickstart leert u hoe u een virtueel netwerk maakt met behulp van de Azure-portal. U implementeert twee virtuele machines (VM's). Vervolgens communiceert u veilig tussen VM's en maakt u verbinding met VM's via internet. Een virtueel netwerk is de fundamentele bouwsteen voor uw privénetwerk in Azure. Hiermee kunnen Azure-bronnen, zoals VM's, veilig met elkaar en met het internet communiceren.

## <a name="prerequisites"></a>Vereisten

* Een Azure-account met een actief abonnement. [Maak er gratis een.](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio)

## <a name="sign-in-to-azure"></a>Aanmelden bij Azure

Meld u aan bij [Azure Portal](https://portal.azure.com).

## <a name="create-a-virtual-network"></a>Een virtueel netwerk maken

1. Selecteer in het menu azure portal de optie **Een resource maken**. Selecteer **netwerknetwerk Netwerken** > **in**Azure Marketplace .

1. Typ of selecteer in **Virtueel netwerk maken** de volgende gegevens:

    | Instelling | Waarde |
    | ------- | ----- |
    | Abonnement | Selecteer uw abonnement.|
    | Resourcegroep | Selecteer **Nieuwe maken**, voer *myResourceGroup* in en selecteer vervolgens **OK**. |
    | Name | Voer *myVirtualNetwork* in. |
    | Locatie | Selecteer **Oost-VS**.|

1. Selecteer **Volgende: IP-adressen**en voor **IPv4-adresruimte**voer *u 10.1.0.0/16 in*.

1. Selecteer **Subnet toevoegen**en voer *vervolgens mijnVirtualSubnet* in voor **subnetnaam** en *10.1.0.0/24* voor **subnetadresbereik**.

1. Selecteer **Toevoegen**en selecteer **Vervolgens Controleren + maken**. Laat de rest als standaard en selecteer **Maken**.

1. Selecteer in **Virtueel netwerk maken**de optie **Maken**.

## <a name="create-virtual-machines"></a>Virtuele machines maken

Maak twee virtuele machines in het virtuele netwerk:

### <a name="create-the-first-vm"></a>De eerste VM maken

1. Selecteer in het menu azure portal de optie **Een resource maken**.

1. Selecteer **Compute** > **Windows Server 2019 Datacenter**in Azure Marketplace . Selecteer **Maken**.

1. Typ of selecteer in **Een virtuele machine maken - Basisprincipes** de volgende gegevens:

    | Instelling | Waarde |
    | ------- | ----- |
    | **Projectdetails** | |
    | Abonnement | Selecteer uw abonnement. |
    | Resourcegroep | Selecteer **myResourceGroup**. U hebt deze resourcegroep in de vorige sectie gemaakt. |
    | **Instantiedetails** |  |
    | Naam van de virtuele machine | Voer *myVm1* in. |
    | Regio | Selecteer **Oost-VS**. |
    | Beschikbaarheidsopties | **Standaard-naar-geen redundantie van de infrastructuur vereist**. |
    | Installatiekopie | Standaard naar **Windows Server 2019 Datacenter**. |
    | Grootte | Standaard standaard **DS1 v2**. |
    | **Beheerdersaccount** |  |
    | Gebruikersnaam | Voer een gebruikersnaam van uw keuze in. |
    | Wachtwoord | Voer een wachtwoord naar keuze in. Het wachtwoord moet minstens 12 tekens lang zijn en moet voldoen aan de [gedefinieerde complexiteitsvereisten](../virtual-machines/windows/faq.md?toc=%2fazure%2fvirtual-network%2ftoc.json#what-are-the-password-requirements-when-creating-a-vm).|
    | Wachtwoord bevestigen | Voer het wachtwoord opnieuw in. |
    | **Binnenkomende poortregels** |  |
    | Openbare poorten voor inkomend verkeer | Selecteer **Geselecteerde poorten toestaan**. |
    | Binnenkomende poorten selecteren | Voer *HTTP (80)* en *RDP (3389) in.* |
    | **Bespaar geld** |  |
    | Hebt u al een Windows-licentie? | Standaard op **Nee**. |

1. Selecteer **Volgende: Schijven**.

1. In **Een virtuele machine maken - Schijven,** de standaardinstellingen behouden en **Volgende: Netwerken selecteren**.

1. Selecteer in **Een virtuele machine maken - Netwerken** de volgende gegevens:

    | Instelling | Waarde |
    | ------- | ----- |
    | Virtueel netwerk | Standaard naar **myVirtualNetwork**. |
    | Subnet | Standaard naar **myVirtualSubnet (10.1.0.0/24)**. |
    | Openbare IP | Standaard naar **(nieuwe) myVm-ip**. |
    | NIC-netwerkbeveiligingsgroep | Standaard naar **Basic**. |
    | Openbare poorten voor inkomend verkeer | Standaard geselecteerde **poorten toestaan**. |
    | Binnenkomende poorten selecteren | Standaard naar **HTTP** en **RDP**.

1. Selecteer **Volgende: Beheer**.

1. Selecteer in **Een virtuele machine maken - Beheer** voor **Opslagaccount voor diagnose** de optie **Nieuw**.

1. Typ of selecteer in **Opslagaccount maken** de volgende gegevens:

    | Instelling | Waarde |
    | ------- | ----- |
    | Name | Voer *myvmstorageaccount* in. Als deze naam wordt aangenomen, maakt u een unieke naam.|
    | Soort account | Standaard opslag **(algemeen gebruik v1)**. |
    | Prestaties | Standaard naar **standaard**. |
    | Replicatie | Standaard voor **lokaal redundante opslag (LRS)**. |

1. Selecteer **OK**en selecteer **Vervolgens Controleren + maken**. U wordt naar de **pagina Controleren + maken geleid,** waar Azure uw configuratie valideert.

1. Wanneer u het **doorgegeven bericht Validatie ziet,** selecteert u **Maken**.

### <a name="create-the-second-vm"></a>De tweede VM maken

Herhaal de procedure in de vorige sectie om een andere virtuele machine te maken.

> [!IMPORTANT]
> Voer *myVm2*in voor de naam van de **virtuele machine**.
>
> Voor **diagnosisopslagaccount**moet u ervoor zorgen dat u **myvmstorageaccount**selecteert in plaats van er een te maken.

## <a name="connect-to-a-vm-from-the-internet"></a>Verbinding maken met een virtuele machine via internet

Nadat u *myVm1*hebt gemaakt, maakt u verbinding met het internet.

1. Zoek en selecteer myVm1 in de *Azure-portal*.

1. Selecteer **Verbinding maken**en vervolgens **RDP**.

    ![Verbinding maken met een virtuele machine](./media/quick-create-portal/connect-to-virtual-machine.png)

    De **connect-pagina** wordt geopend.

1. Selecteer **RDP-bestand downloaden**. In Azure wordt een *RDP*-bestand (Remote Desktop Protocol) gemaakt en het bestand wordt gedownload naar de computer.

1. Open het RDP-bestand. Selecteer **Verbinding maken** wanneer hierom wordt gevraagd.

1. Voer de gebruikersnaam en het wachtwoord in dat u hebt opgegeven bij het maken van de vm.

    > [!NOTE]
    > Mogelijk moet u **Meer opties** > selecteren**Gebruik een ander account**om de referenties op te geven die u hebt ingevoerd toen u de vm hebt gemaakt.

1. Selecteer **OK**.

1. U een certificaatwaarschuwing ontvangen wanneer u zich aanmeldt. Als er een certificaatwaarschuwing wordt weergegeven, selecteert u **Ja** of **Doorgaan**.

1. Wanneer het VM-bureaublad wordt weergegeven, minimaliseert u het om terug te gaan naar het lokale bureaublad.

## <a name="communicate-between-vms"></a>Communicatie tussen virtuele machines

1. Open PowerShell in het externe bureaublad van *myVm1*.

1. Voer `ping myVm2` in.

    U ontvangt een bericht dat vergelijkbaar is met deze uitvoer:

    ```output
    Pinging myVm2.0v0zze1s0uiedpvtxz5z0r0cxg.bx.internal.clouda
    Request timed out.
    Request timed out.
    Request timed out.
    Request timed out.

    Ping statistics for 10.1.0.5:
    Packets: Sent = 4, Received = 0, Lost = 4 (100% loss),
    ```

    De `ping` mislukt omdat `ping` gebruikmaakt van het Internet Control Message Protocol (ICMP). ICMP wordt standaard niet toegestaan via de Windows-firewall.

1. Voer de volgende opdracht in om *myVm2* toe te staan *myVm1* in een latere stap te pingen:

    ```powershell
    New-NetFirewallRule –DisplayName "Allow ICMPv4-In" –Protocol ICMPv4
    ```

    Met deze opdracht kan ICMP binnenkomen via de Windows-firewall:

1. Sluit de externe bureaubladverbinding met *myVm1*.

1. Voer de stappen in [Verbinding maken met een virtuele machine via internet](#connect-to-a-vm-from-the-internet) opnieuw uit, maar maak nu verbinding met *myVm2*.

1. Voer `ping myvm1` in vanaf een opdrachtprompt.

    Er wordt een dergelijk bericht weergegeven:

    ```output
    Pinging myVm1.0v0zze1s0uiedpvtxz5z0r0cxg.bx.internal.cloudapp.net [10.1.0.4] with 32 bytes of data:
    Reply from 10.1.0.4: bytes=32 time=1ms TTL=128
    Reply from 10.1.0.4: bytes=32 time<1ms TTL=128
    Reply from 10.1.0.4: bytes=32 time<1ms TTL=128
    Reply from 10.1.0.4: bytes=32 time<1ms TTL=128

    Ping statistics for 10.1.0.4:
        Packets: Sent = 4, Received = 4, Lost = 0 (0% loss),
    Approximate round trip times in milli-seconds:
        Minimum = 0ms, Maximum = 1ms, Average = 0ms
    ```

    U ontvangt antwoorden van *myVm1,* omdat u ICMP hebt toegestaan via de Windows-firewall op de *myVm1* VM in stap 3.

1. Sluit de externe bureaubladverbinding met *myVm2*.

## <a name="clean-up-resources"></a>Resources opschonen

In deze snelstart hebt u een standaard virtueel netwerk en twee virtuele machines gemaakt. U hebt vanaf het internet verbinding gemaakt met één VM en veilig gecommuniceerd tussen de twee VM's.

Wanneer u klaar bent met het virtuele netwerk en de VM's, verwijdert u de brongroep en alle bronnen die deze bevat:

1. Zoeken naar en selecteer *myResourceGroup*.

1. Selecteer **Resourcegroep verwijderen**.

1. Voer *myResourceGroup* in voor **TYP DE RESOURCEGROEPNAAM** en selecteer **Verwijderen**.

## <a name="next-steps"></a>Volgende stappen

Zie [Een virtueel netwerk maken, wijzigen of verwijderen](manage-virtual-network.md)voor meer informatie over virtuele netwerkinstellingen.

Azure maakt standaard veilige communicatie tussen VM's mogelijk. Azure staat alleen inkomende externe desktopverbindingen met Windows VM's vanaf het internet toe. Zie [Netwerkverkeer filteren](tutorial-filter-network-traffic.md)voor meer informatie over typen VM-netwerkcommunicatie.
