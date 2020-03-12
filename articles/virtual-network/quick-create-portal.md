---
title: 'Een virtueel netwerk maken: quickstart - Azure-portal'
titleSuffix: Azure Virtual Network
description: "Snelstartgids: een virtueel netwerk maken in de Azure Portal. Met deze netwerken kunnen Azure-resources, zoals Vm's, veilig communiceren met elkaar en Internet."
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
ms.sourcegitcommit: be53e74cd24bbabfd34597d0dcb5b31d5e7659de
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/11/2020
ms.locfileid: "79129136"
---
# <a name="quickstart-create-a-virtual-network-using-the-azure-portal"></a>Snelstart: een virtueel netwerk maken met Azure Portal

In deze Quick Start leert u hoe u een virtueel netwerk maakt met behulp van de Azure Portal. U implementeert twee virtuele machines (Vm's). Vervolgens communiceert u veilig tussen Vm's en maakt u verbinding met Vm's via internet. Een virtueel netwerk is de fundamentele bouw steen voor uw particuliere netwerk in Azure. Hierdoor kunnen Azure-resources, zoals Vm's, veilig communiceren met elkaar en met internet.

## <a name="prerequisites"></a>Vereisten

* Een Azure-account met een actief abonnement. [Maak er gratis een](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio).

## <a name="sign-in-to-azure"></a>Aanmelden bij Azure

Meld u aan bij de [Azure-portal](https://portal.azure.com).

## <a name="create-a-virtual-network"></a>Een virtueel netwerk maken

1. Selecteer in het menu Azure Portal de optie **een resource maken**. Selecteer in azure Marketplace **netwerk** > **virtueel netwerk**.

1. Typ of selecteer in **Virtueel netwerk maken** de volgende gegevens:

    | Instelling | Waarde |
    | ------- | ----- |
    | Abonnement | Selecteer uw abonnement.|
    | Resourcegroep | Selecteer **Nieuwe maken**, voer *myResourceGroup* in en selecteer vervolgens **OK**. |
    | Naam | Voer *myVirtualNetwork* in. |
    | Locatie | Selecteer **VS - oost**.|

1. Selecteer **volgende: IP-adressen**en voer voor **IPv4-adres ruimte** *10.1.0.0/16*in.

1. Selecteer **subnet toevoegen**en voer *MyVirtualSubnet* in als **subnetnaam** en *10.1.0.0/24* voor het **adres bereik**van het subnet.

1. Selecteer **toevoegen**en selecteer vervolgens **controleren + maken**. Laat de rest als standaard en selecteer **maken**.

1. Selecteer **maken**in **virtueel netwerk maken**.

## <a name="create-virtual-machines"></a>Virtuele machines maken

Twee virtuele machines in het virtuele netwerk maken:

### <a name="create-the-first-vm"></a>De eerste VM maken

1. Selecteer in het menu Azure Portal de optie **een resource maken**.

1. Selecteer in azure Marketplace **compute** > **Windows Server 2019 Data Center**. Selecteer **Maken**.

1. Typ of selecteer in **Een virtuele machine maken - Basisprincipes** de volgende gegevens:

    | Instelling | Waarde |
    | ------- | ----- |
    | **Project Details** | |
    | Abonnement | Selecteer uw abonnement. |
    | Resourcegroep | Selecteer **myResourceGroup**. U hebt deze resource groep gemaakt in de vorige sectie. |
    | **Exemplaar Details** |  |
    | Naam van de virtuele machine | Voer *myVm1* in. |
    | Regio | Selecteer **VS - oost**. |
    | Beschikbaarheidsopties | Standaard is **er geen infrastructuur redundantie vereist**. |
    | Installatiekopie | Standaard naar **Windows Server 2019 Data Center**. |
    | Grootte | Standaard ingesteld op **standaard ds1 versie 2**. |
    | **Administrator-account** |  |
    | Gebruikersnaam | Voer een gebruikers naam van uw keuze in. |
    | Wachtwoord | Voer een wachtwoord naar keuze in. Het wachtwoord moet minstens 12 tekens lang zijn en moet voldoen aan de [gedefinieerde complexiteitsvereisten](../virtual-machines/windows/faq.md?toc=%2fazure%2fvirtual-network%2ftoc.json#what-are-the-password-requirements-when-creating-a-vm).|
    | Wachtwoord bevestigen | Voer het wacht woord opnieuw in. |
    | **Binnenkomende poort regels** |  |
    | Openbare poorten voor inkomend verkeer | Selecteer **Geselecteerde poorten toestaan**. |
    | Binnenkomende poorten selecteren | Voer *http (80)* en *RDP (3389)* in. |
    | **Geld besparen** |  |
    | Hebt u al een Windows-licentie? | Standaard ingesteld op **Nee**. |

1. Selecteer **volgende: schijven**.

1. In **een virtuele machine maken-schijven**, behoud de standaard instellingen en selecteer **volgende: netwerken**.

1. Selecteer in **Een virtuele machine maken - Netwerken** de volgende gegevens:

    | Instelling | Waarde |
    | ------- | ----- |
    | Virtueel netwerk | Standaard ingesteld op **myVirtualNetwork**. |
    | Subnet | Standaard ingesteld op **myVirtualSubnet (10.1.0.0/24)** . |
    | Openbare IP | Standaard naar **(nieuw) myVm-IP**. |
    | NIC-netwerk beveiligings groep | Standaard ingesteld op **basis**. |
    | Openbare poorten voor inkomend verkeer | Standaard instelling voor het **toestaan van geselecteerde poorten**. |
    | Binnenkomende poorten selecteren | Standaard ingesteld op **http** en **RDP**.

1. Selecteer **volgende: beheer**.

1. Selecteer in **Een virtuele machine maken - Beheer** voor **Opslagaccount voor diagnose** de optie **Nieuw**.

1. Typ of selecteer in **Opslagaccount maken** de volgende gegevens:

    | Instelling | Waarde |
    | ------- | ----- |
    | Naam | Voer *myvmstorageaccount* in. Als deze naam wordt gebruikt, maakt u een unieke naam.|
    | Soort account | Standaard voor **opslag (algemeen gebruik v1)** . |
    | Prestaties | Standaard ingesteld op **standaard**. |
    | Replicatie | Standaard naar **lokaal redundante opslag (LRS)** . |

1. Selecteer **OK**en selecteer vervolgens **bekijken + maken**. U gaat naar de pagina **controleren en maken** waar Azure uw configuratie valideert.

1. Wanneer u het bericht **door gegeven validatie** ziet, selecteert u **maken**.

### <a name="create-the-second-vm"></a>De tweede VM maken

Herhaal de procedure in de vorige sectie om een andere virtuele machine te maken.

> [!IMPORTANT]
> Voer *myVm2*in voor de naam van de **virtuele machine**.
>
> Zorg ervoor dat u voor **Diagnostic Storage**-account **myvmstorageaccount**selecteert, in plaats van er een te maken.

## <a name="connect-to-a-vm-from-the-internet"></a>Verbinding maken met een virtuele machine via internet

Nadat u *myVm1*hebt gemaakt, maakt u verbinding met internet.

1. Zoek in het Azure Portal naar en selecteer *myVm1*.

1. Selecteer **verbinding maken**en vervolgens **RDP**.

    ![Verbinding maken met een virtuele machine](./media/quick-create-portal/connect-to-virtual-machine.png)

    De pagina **verbinding maken** wordt geopend.

1. Selecteer **RDP-bestand downloaden**. Azure maakt een Remote Desktop Protocol-bestand ( *.rdp*) en downloadt het bestand naar uw computer.

1. Open het RDP-bestand. Selecteer **Verbinding maken** wanneer hierom wordt gevraagd.

1. Voer de gebruikers naam en het wacht woord in die u hebt opgegeven bij het maken van de virtuele machine.

    > [!NOTE]
    > Mogelijk moet u **Meer opties** > **Een ander account gebruiken** selecteren om de referenties op te geven die u hebt ingevoerd tijdens het maken van de VM.

1. Selecteer **OK**.

1. Er wordt mogelijk een certificaat waarschuwing weer gegeven wanneer u zich aanmeldt. Als er een certificaatwaarschuwing wordt weergegeven, selecteert u **Ja** of **Doorgaan**.

1. Wanneer het VM-bureaublad wordt weergegeven, minimaliseert u het om terug te gaan naar het lokale bureaublad.

## <a name="communicate-between-vms"></a>Communiceren tussen VM's

1. Open PowerShell in het externe bureaublad van *myVm1*.

1. Voer `ping myVm2` in.

    Er wordt een bericht weer gegeven dat vergelijkbaar is met deze uitvoer:

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

    Met deze opdracht wordt ICMP-inkomend verkeer via de Windows firewall toegestaan:

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

    U ontvangt antwoorden van *myVm1*, omdat u ICMP hebt toegestaan via de Windows Firewall op de virtuele *myVm1* -machine in stap 3.

1. Sluit de externe bureaubladverbinding met *myVm2*.

## <a name="clean-up-resources"></a>Resources opschonen

In deze snelstart hebt u een standaard virtueel netwerk en twee virtuele machines gemaakt. U hebt verbinding gemaakt met één virtuele machine via internet en u kunt een beveiligde communicatie tussen de twee virtuele machines.

Wanneer u klaar bent met het virtuele netwerk en de Vm's, verwijdert u de resource groep en alle resources die deze bevat:

1. Zoek en selecteer *myResourceGroup*.

1. Selecteer **Resourcegroep verwijderen**.

1. Voer *myResourceGroup* in voor **TYP DE RESOURCEGROEPNAAM** en selecteer **Verwijderen**.

## <a name="next-steps"></a>Volgende stappen

Zie [een virtueel netwerk maken, wijzigen of verwijderen](manage-virtual-network.md)voor meer informatie over de instellingen van het virtuele netwerk.

Standaard staat Azure beveiligde communicatie tussen Vm's toe. Azure staat alleen binnenkomende externe bureaublad verbindingen met Windows Vm's via Internet toe. Zie [netwerk verkeer filteren](tutorial-filter-network-traffic.md)voor meer informatie over typen VM-netwerk communicatie.
