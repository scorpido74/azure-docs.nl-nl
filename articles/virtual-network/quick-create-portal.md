---
title: 'Een virtueel netwerk maken: quickstart - Azure-portal'
titlesuffix: Azure Virtual Network
description: In deze snelstart leert u hoe u een virtueel netwerk maakt met Azure Portal. Met een virtueel netwerk kunnen Azure-resources, zoals virtuele machines, veilig communiceren met elkaar en met Internet
services: virtual-network
documentationcenter: virtual-network
author: KumudD
tags: azure-resource-manager
Customer intent: I want to create a virtual network so that virtual machines can securely communicate with each other and with the internet.
ms.service: virtual-network
ms.devlang: na
ms.topic: quickstart
ms.tgt_pltfrm: virtual-network
ms.workload: infrastructure
ms.date: 07/08/2019
ms.author: kumud
ms.openlocfilehash: f95174efbffedd09946f5910a39285e7bec062c1
ms.sourcegitcommit: 0b1a4101d575e28af0f0d161852b57d82c9b2a7e
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 10/30/2019
ms.locfileid: "73161496"
---
# <a name="quickstart-create-a-virtual-network-using-the-azure-portal"></a>Snelstart: een virtueel netwerk maken met Azure Portal

Een virtueel netwerk is de fundamentele bouw steen voor uw particuliere netwerk in Azure. Hierdoor kunnen Azure-resources, zoals virtuele machines (Vm's), veilig communiceren met elkaar en met internet. In deze Quick Start leert u hoe u een virtueel netwerk maakt met behulp van de Azure Portal. Vervolgens kunt u twee virtuele machines in het virtuele netwerk implementeren, veilig communiceren tussen de twee Vm's en verbinding maken met de virtuele machines via internet.


Als u nog geen abonnement op Azure hebt, maak dan nu een [gratis account](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

## <a name="sign-in-to-azure"></a>Aanmelden bij Azure

Meld u aan bij de [Azure-portal](https://portal.azure.com).

## <a name="create-a-virtual-network"></a>Maak een virtueel netwerk

1. Selecteer linksboven in het scherm **Een resource maken** > **Netwerken** > **Virtueel netwerk**.

1. Typ of selecteer in **Virtueel netwerk maken** de volgende gegevens:

    | Instelling | Waarde |
    | ------- | ----- |
    | Naam | Voer *myVirtualNetwork* in. |
    | Adresruimte | Voer *10.1.0.0/16* in. |
    | Abonnement | Selecteer uw abonnement.|
    | Resourcegroep | Selecteer **Nieuwe maken**, voer *myResourceGroup* in en selecteer vervolgens **OK**. |
    | Locatie | Selecteer **US - oost**.|
    | Subnet - naam | Voer *myVirtualSubnet* in. |
    | Subnet - adresbereik | Voer *10.1.0.0/24* in. |

1. Laat de rest als standaard en selecteer **maken**.

## <a name="create-virtual-machines"></a>Virtuele machines maken

Maak twee virtuele machines in het virtuele netwerk:

### <a name="create-the-first-vm"></a>De eerste VM maken

1. Selecteer in de linkerbovenhoek van het scherm **een resource maken** > **reken** > **Windows Server 2019 Data Center**.

1. Typ of selecteer in **Een virtuele machine maken - Basisprincipes** de volgende gegevens:

    | Instelling | Waarde |
    | ------- | ----- |
    | **PROJECTGEGEVENS** | |
    | Abonnement | Selecteer uw abonnement. |
    | Resourcegroep | Selecteer **myResourceGroup**. U hebt dit gemaakt in de vorige sectie. |
    | **INSTANTIEDETAILS** |  |
    | Naam van de virtuele machine | Voer *myVm1* in. |
    | Regio | Selecteer **US - oost**. |
    | Beschikbaarheidsopties | Laat de standaardwaarde **Geen infrastructuurredundantie vereist** staan. |
    | afbeelding | Verlaat het standaard **Windows Server 2019 Data Center**. |
    | Grootte | Laat de standaardwaarde **Standard DS1 v2** staan. |
    | **ADMINISTRATOR-ACCOUNT** |  |
    | Gebruikersnaam | Voer een gebruikers naam van uw keuze in. |
    | Wachtwoord | Voer een wachtwoord naar keuze in. Het wachtwoord moet minstens 12 tekens lang zijn en moet voldoen aan de [gedefinieerde complexiteitsvereisten](../virtual-machines/windows/faq.md?toc=%2fazure%2fvirtual-network%2ftoc.json#what-are-the-password-requirements-when-creating-a-vm).|
    | Wachtwoord bevestigen | Voer het wachtwoord opnieuw in. |
    | **REGELS VOOR BINNENKOMENDE POORT** |  |
    | Openbare poorten voor inkomend verkeer | Laat de standaardwaarde **Geen** staan. |
    | **GELD BESPAREN** |  |
    | Hebt u al een Windows-licentie? | Laat de standaardwaarde **Nee** staan. |

1. Selecteer **volgende: schijven**.

1. In **een virtuele machine maken-schijven**, de standaard instellingen behouden en **volgende selecteren: netwerken**.

1. Selecteer in **Een virtuele machine maken - Netwerken** de volgende gegevens:

    | Instelling | Waarde |
    | ------- | ----- |
    | Virtueel netwerk | Laat de standaardwaarde **myVirtualNetwork** staan. |
    | Subnet | Handhaaf de standaardinstelling **myVirtualSubnet (10.1.0.0/24)** . |
    | Openbare IP | Handhaaf de standaardinstelling **(new) myVm-ip**. |
    | Openbare poorten voor inkomend verkeer | Selecteer **Geselecteerde poorten toestaan**. |
    | Binnenkomende poorten selecteren | Selecteer **HTTP** en **RDP**.

1. Selecteer **volgende: beheer**.

1. Selecteer in **Een virtuele machine maken - Beheer** voor **Opslagaccount voor diagnose** de optie **Nieuw**.

1. Typ of selecteer in **Opslagaccount maken** de volgende gegevens:

    | Instelling | Waarde |
    | ------- | ----- |
    | Naam | Voer *myvmstorageaccount* in. Als deze naam wordt gebruikt, maakt u een unieke naam.|
    | Soort account | Laat de standaardwaarde **Storage (algemeen gebruik v1)** staan. |
    | Prestaties | Laat de standaardwaarde **Standard** staan. |
    | Replicatie | Laat de standaardwaarde **Lokaal redundante opslag (LRS)** staan. |

1. Selecteer **OK**

1. Selecteer **Controleren + maken**. U gaat naar de pagina **controleren en maken** waar Azure uw configuratie valideert.

1. Wanneer u het bericht **door gegeven validatie** ziet, selecteert u **maken**.

### <a name="create-the-second-vm"></a>De tweede VM maken

1. Voer bovenstaande stappen 1 tot en met 9 uit.

    > [!NOTE]
    > In stap 2 voor de **naam van de virtuele machine** geeft u *myVm2* op.
    >
    > In stap 7 voor **Opslagaccount voor diagnose** selecteert u **myvmstorageaccount**.

1. Selecteer **Controleren + maken**. De pagina **Beoordelen en maken** wordt weergegeven en de configuratie wordt gevalideerd in Azure.

1. Wanneer u het bericht **door gegeven validatie** ziet, selecteert u **maken**.

## <a name="connect-to-a-vm-from-the-internet"></a>Verbinding maken met een virtuele machine via internet

Nadat u *myVm1*hebt gemaakt, maakt u verbinding met internet.

1. Voer in de zoekbalk van de portal *myVm1* in.

1. Selecteer de knop **Verbinding maken**.

    ![Verbinding maken met een virtuele machine](./media/quick-create-portal/connect-to-virtual-machine.png)

    Na het selecteren van de knop **Verbinden** wordt **Verbinden met virtuele machine** geopend.

1. Selecteer **RDP-bestand downloaden**. Azure maakt een Remote Desktop Protocol-bestand ( *.rdp*) en downloadt het bestand naar uw computer.

1. Open het gedownloade *RDP*-bestand.

    1. Selecteer **Verbinding maken** wanneer hierom wordt gevraagd.

    1. Voer de gebruikers naam en het wacht woord in die u hebt opgegeven bij het maken van de virtuele machine.

        > [!NOTE]
        > Mogelijk moet u **Meer opties** > **Een ander account gebruiken** selecteren om de referenties op te geven die u hebt ingevoerd tijdens het maken van de VM.

1. Selecteer **OK**.

1. Er wordt mogelijk een certificaatwaarschuwing weergegeven tijdens het aanmelden. Als er een certificaatwaarschuwing wordt weergegeven, selecteert u **Ja** of **Doorgaan**.

1. Wanneer het VM-bureaublad wordt weergegeven, minimaliseert u het om terug te gaan naar het lokale bureaublad.

## <a name="communicate-between-vms"></a>Communicatie tussen virtuele machines

1. Open PowerShell in het externe bureaublad van *myVm1*.

1. Voer `ping myVm2` in.

    U ontvangt een bericht dat er ongeveer als volgt uitziet:

    ```powershell
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

    ```powershell
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

Wanneer u klaar bent met het virtuele netwerk en de Vm's, verwijdert u de resource groep en alle resources die deze bevat:

1. Voer *myResourceGroup* in het **zoekvak** boven aan de portal in en selecteer **myResourceGroup** in de zoek resultaten.

1. Selecteer **Resourcegroep verwijderen**.

1. Voer *myResourceGroup* in voor **TYP DE RESOURCEGROEPNAAM** en selecteer **Verwijderen**.

## <a name="next-steps"></a>Volgende stappen

In deze Quick Start hebt u een standaard virtueel netwerk en twee virtuele machines gemaakt. U hebt verbinding gemaakt met één virtuele machine via internet en u kunt een beveiligde communicatie tussen de twee virtuele machines. Zie [Een virtueel netwerk beheren](manage-virtual-network.md) voor meer informatie over instellingen voor virtuele netwerken.

Standaard staat Azure onbeperkte beveiligde communicatie tussen Vm's toe. Daarentegen zijn alleen inkomende verbindingen met extern bureaublad met Windows-VM's via internet toegestaan. Ga naar de zelfstudie [Netwerkverkeer filteren](tutorial-filter-network-traffic.md) voor meer informatie over het configureren van verschillende typen VM-netwerkcommunicatie.
