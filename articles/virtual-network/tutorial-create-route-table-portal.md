---
title: Netwerkverkeer routeren - zelfstudie - Azure-portal
titlesuffix: Azure Virtual Network
description: In deze zelfstudie leert u netwerkverkeer te routeren met een routetabel met behulp van Azure Portal.
services: virtual-network
documentationcenter: virtual-network
author: KumudD
Customer intent: I want to route traffic from one subnet, to a different subnet, through a network virtual appliance.
ms.service: virtual-network
ms.devlang: azurecli
ms.topic: tutorial
ms.tgt_pltfrm: virtual-network
ms.workload: infrastructure
ms.date: 03/13/2020
ms.author: kumud
ms.openlocfilehash: 854bf2d1ed2155290c2ecebd17695049fdd0c8bb
ms.sourcegitcommit: 7e04a51363de29322de08d2c5024d97506937a60
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/14/2020
ms.locfileid: "81314292"
---
# <a name="tutorial-route-network-traffic-with-a-route-table-using-the-azure-portal"></a>Zelfstudie: Netwerkverkeer routeren met een routetabel met behulp van Azure Portal

In Azure wordt verkeer standaard geretourneerd tussen alle subnetten in een virtueel netwerk. U kunt uw eigen routes maken om de standaardroutering van Azure te overschrijven. Aangepaste routes zijn handig wanneer u bijvoorbeeld verkeer tussen subnetten wilt routeren via een netwerkvirtueel toestel (NVA). In deze zelfstudie leert u het volgende:

> [!div class="checklist"]
> * Een NVA maken voor het routeren van verkeer
> * Een routetabel maken
> * Een route maken
> * Een routetabel aan een subnet koppelen
> * Virtuele machines (VM's) implementeren in verschillende subnetten
> * Verkeer van het ene subnet naar het andere leiden via een NVA

In deze zelfstudie wordt de [Azure-portal gebruikt.](https://portal.azure.com) U ook [Azure CLI](tutorial-create-route-table-cli.md) of [Azure PowerShell](tutorial-create-route-table-powershell.md)gebruiken.

Als u geen Azure-abonnement hebt, maakt u een [gratis account](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) voordat u begint.

## <a name="create-an-nva"></a>Een NVA maken

Netwerk virtuele apparaten (NVA's) zijn virtuele machines die helpen met netwerkfuncties, zoals routing en firewall optimalisatie. In deze zelfstudie wordt ervan uitgegaan dat u **Windows Server 2016 Datacenter** gebruikt. Als u wilt, kunt u een ander besturingssysteem selecteren.

1. Selecteer in het menu [azure portal](https://portal.azure.com) of op **de** startpagina de optie Een **bron maken**.

1. Kies **Beveiliging** > **Windows Server 2016-datacenter**.

    ![Windows Server 2016-datacenter, een VM, Azure-portal maken](./media/tutorial-create-route-table-portal/vm-ws2016-datacenter.png)

1. Voer op de pagina **Een virtuele machine maken** onder **Basisbeginselen**deze informatie in of selecteer deze:

    | Sectie | Instelling | Actie |
    | ------- | ------- | ----- |
    | **Projectdetails** | Abonnement | Kies uw abonnement. |
    | | Resourcegroep | Selecteer **Nieuwe maken**, voer *myResourceGroup* in en selecteer **OK**. |
    | **Instantiedetails** | Naam van de virtuele machine | Voer *myVmNva* in. |
    | | Regio | Kies **(US) East US**. |
    | | Beschikbaarheidsopties | Kies **Geen redundantie voor de infrastructuur vereist**. |
    | | Installatiekopie | Kies **Windows Server 2016 Datacenter**. |
    | | Grootte | Houd de standaard standaard, **Standaard DS1 v2**. |
    | **Beheerdersaccount** | Gebruikersnaam | Voer een gebruikersnaam naar keuze in. |
    | | Wachtwoord | Voer een wachtwoord in naar keuze, dat ten minste 12 tekens lang moet zijn en moet voldoen aan de [gedefinieerde complexiteitsvereisten.](../virtual-machines/windows/faq.md?toc=%2fazure%2fvirtual-network%2ftoc.json#what-are-the-password-requirements-when-creating-a-vm) |
    | | Wachtwoord bevestigen | Voer het wachtwoord opnieuw in. |
    | **Binnenkomende poortregels** | Openbare poorten voor inkomend verkeer | Kies **Geen**. |
    | **Bespaar geld** | Heb je al een Windows Server-licentie? | Kies **Nee.** |

    ![Basisbeginselen, Een virtuele machine maken, Azure-portal](./media/tutorial-create-route-table-portal/basics-create-virtual-machine.png)

    Selecteer **vervolgens Volgende: >schijven **.

1. Selecteer **onder Schijven**de instellingen die geschikt zijn voor uw behoeften en selecteer **Volgende: >** netwerken.

1. In het kader **van Netwerken**:

    1. Selecteer Voor **virtueel netwerk**de optie **Nieuw maken**.
    
    1. Voer in het dialoogvenster **Virtueel netwerk maken** onder **Naam** *myVirtualNetwork*in .

    1. Vervang **in adresruimte**het bestaande adresbereik door *10.0.0.0/16*.

    1. Selecteer **in Subnetten**het pictogram **Verwijderen** om het bestaande subnet te verwijderen en voer vervolgens de volgende combinaties van **subnetnaam** en **adresbereik in**. Zodra een geldige naam en bereik is ingevoerd, verschijnt er een nieuwe lege rij onder.

        | Subnetnaam | Adresbereik |
        | ----------- | ------------- |
        | *Public* | *10.0.0.0/24* |
        | *Privé* | *10.0.1.0/24* |
        | *DMZ* | *10.0.2.0/24* |

    1. Selecteer **OK** om het dialoogvenster af te sluiten.

    1. Kies **in Subnet** **DMZ (10.0.2.0/24)**.

    1. Kies **in Public IP** **Geen,** omdat deze vm geen verbinding maakt via internet.

    1. Selecteer **Volgende : Beheer >**.

1. Onder **beheer**:

    1. Selecteer in **het opslagaccount Diagnostische gegevens**de optie Nieuw **maken**.
    
    1. Voer deze gegevens in het dialoogvenster **Opslagaccount maken** in of selecteer deze:

        | Instelling | Waarde |
        | ------- | ----- |
        | Name | *mynvastorageaccount* |
        | Soort account | **Opslag (algemeen doel v1)** |
        | Prestaties | **Standaard** |
        | Replicatie | **Lokaal redundante opslag (LRS)** |
    
    1. Selecteer **OK** om het dialoogvenster af te sluiten.

    1. Selecteer **Controleren + maken**. U wordt naar de pagina **Controleren + maken** geleid en Azure valideert uw configuratie.

1. Wanneer u het **doorgegeven bericht Validatie ziet,** selecteert u **Maken**.

    Het maken van de virtuele machine duurt een paar minuten. Wacht tot Azure klaar is met het maken van de VM. Op de pagina **Uw implementatie is aan de gang,** ziet u implementatiegegevens.

1. Als de VM klaar is, selecteert u **Ga naar resource**.

## <a name="create-a-route-table"></a>Een routetabel maken

1. Selecteer in het menu [azure portal](https://portal.azure.com) of op **de** startpagina de optie Een **bron maken**.

2. Voer in het zoekvak de *tabel Route*in . Wanneer **de tabel Route** wordt weergegeven in de zoekresultaten, selecteert u deze.

3. Selecteer op de pagina **Routetabel** de optie **Maken**.

4. Typ of selecteer in **Routetabel maken** de volgende gegevens:

    | Instelling | Waarde |
    | ------- | ----- |
    | Name | *myRouteTablePublic* |
    | Abonnement | Uw abonnement |
    | Resourcegroep | **myResourceGroup** |
    | Locatie | **(VS) Oost-VS** |
    | Verspreiding van virtuele netwerkgatewayroutes | **Ingeschakeld** |

    ![Routetabel maken, Azure-portal](./media/tutorial-create-route-table-portal/create-route-table.png)

5. Selecteer **Maken**.

## <a name="create-a-route"></a>Een route maken

1. Ga naar de [Azure-portal](https://portal.azure.com) om uw routetabel te beheren. Routetabellen zoeken **Route tables**en selecteren .

1. Kies de naam van uw routetabel **(myRouteTablePublic).**

1. Kies **Routes** > **Toevoegen**.

    ![Route, routetabel, Azure-portal toevoegen](./media/tutorial-create-route-table-portal/add-route.png)

1. Typ of selecteer in **Route toevoegen** de volgende gegevens:

    | Instelling | Waarde |
    | ------- | ----- |
    | Routenaam | *ToPrivateSubnet* |
    | Adresvoorvoegsel | *10.0.1.0/24* (het adresbereik van het eerder gemaakte privé-subnet) *Private* |
    | Volgend hoptype | **Virtueel toestel** |
    | Adres van de volgende hop | *10.0.2.4* (een adres binnen het adresbereik van het *DMZ-subnet)* |

1. Selecteer **OK**.

## <a name="associate-a-route-table-to-a-subnet"></a>Een routetabel aan een subnet koppelen

1. Ga naar de [Azure-portal](https://portal.azure.com) om uw virtuele netwerk te beheren. Zoeken naar virtuele netwerken en selecteer **deze.**

1. Kies de naam van uw virtuele netwerk **(myVirtualNetwork).**

1. Kies **subnetten**in de menubalk van het virtuele netwerk.

1. Kies **Openbaar**in de subnetlijst van het virtuele netwerk .

1. Kies **in de tabel Route**de routetabel die u hebt gemaakt **(myRouteTablePublic)** en selecteer **Opslaan** om uw routetabel te koppelen aan het subnet *Openbaar.*

    ![Routetabel, subnetlijst, virtueel netwerk, Azure-portal koppelen](./media/tutorial-create-route-table-portal/associate-route-table.png)

## <a name="turn-on-ip-forwarding"></a>Doorsturen via IP inschakelen

Schakel vervolgens IP forwarding in voor uw nieuwe NVA virtuele machine, *myVmNva.* Wanneer Azure netwerkverkeer naar *myVmNva*verzendt, als het verkeer is bestemd voor een ander IP-adres, stuurt IP-forwarding het verkeer naar de juiste locatie.

1. Ga naar de [Azure-portal](https://portal.azure.com) om uw VM te beheren. Zoeken naar virtuele machines en selecteer **deze**.

1. Kies de naam van uw VM **(myVmNva).**

1. Selecteer **netwerken**in de menubalk van uw NVA-virtuele machine.

1. Selecteer **myvmnva123**. Dit is de netwerkinterface die in Azure is gemaakt voor uw VM. Azure voegt getallen toe om een unieke naam te garanderen.

    ![Netwerken, network virtual appliance (NVA) virtuele machine (VM), Azure portal](./media/tutorial-create-route-table-portal/virtual-machine-networking.png)

1. Selecteer **IP-configuraties**in de menubalk van de netwerkinterface.

1. Stel op de pagina **IP-configuraties** **IP-doorsturen** **in op Ingeschakeld**en selecteer **Opslaan**.

    ![IP-forwarding, IP-configuraties, netwerkinterface, virtual appliance (NVA) virtuele machine (VM), Azure-portal inschakelen](./media/tutorial-create-route-table-portal/enable-ip-forwarding.png)

## <a name="create-public-and-private-virtual-machines"></a>Virtuele machines maken, openbaar en privé

Maak een openbare VM en een privé-VM in het virtuele netwerk. Later gebruikt u deze om te zien dat het *Openbare* subnetverkeer in Azure via de NVA naar het *Privé*subnet wordt gerouteerd.

Als u de openbare VM en de private VM wilt maken, volgt u de stappen [van Een NVA](#create-an-nva) maken. U hoeft niet te wachten tot de implementatie is voltooid of naar de VM-bron te gaan. U gebruikt de meeste van dezelfde instellingen, behalve zoals hieronder beschreven.

Voordat u **Maken** selecteert om de openbare of private VM te maken, gaat u naar de volgende twee subsecties[(Openbare VM](#public-vm) en Private [VM),](#private-vm)waarin de waarden worden weergegeven die anders moeten zijn. U doorgaan naar de volgende sectie[(Routeverkeer via een NVA)](#route-traffic-through-an-nva)nadat Azure is voltooid met het implementeren van beide VM's.

### <a name="public-vm"></a>Openbare VM

| Tab | Instelling | Waarde |
| --- | ------- | ----- |
| Basisbeginselen | Resourcegroep | **myResourceGroup** |
| | Naam van de virtuele machine | *myVmPublic* |
| | Openbare poorten voor inkomend verkeer | **Geselecteerde poorten toestaan** |
| | Binnenkomende poorten selecteren | **RDP** |
| Netwerken | Virtueel netwerk | **myVirtualNetwork** |
| | Subnet | **Openbaar (10.0.0.0/24)** |
| | Openbaar IP-adres | De standaardinstelling |
| Beheer | Opslagaccount voor diagnose | **mynvastorageaccount** |

### <a name="private-vm"></a>Privé-VM

| Tab | Instelling | Waarde |
| --- | ------- | ----- |
| Basisbeginselen | Resourcegroep | **myResourceGroup** |
| | Naam van de virtuele machine | *myVmPrivate* |
| | Openbare poorten voor inkomend verkeer | **Geselecteerde poorten toestaan** |
| | Binnenkomende poorten selecteren | **RDP** |
| Netwerken | Virtueel netwerk | **myVirtualNetwork** |
| | Subnet | **Privé (10.0.1.0/24)** |
| | Openbaar IP-adres | De standaardinstelling |
| Beheer | Opslagaccount voor diagnose | **mynvastorageaccount** |

## <a name="route-traffic-through-an-nva"></a>Verkeer routeren via een NVA

### <a name="sign-in-to-myvmprivate-over-remote-desktop"></a>Aanmelden bij myVmPrivate via het externe bureaublad

1. Ga naar de [Azure-portal](https://portal.azure.com) om uw privé-virtuele vm te beheren. Zoeken naar virtuele machines en selecteer **deze**.

1. Kies de naam van uw privé-vm **(myVmPrivate).**

1. Selecteer in de menubalk van de VM **verbinding** maken om een verbinding met het externe bureaublad met de privé-virtuele virtuele- virtuele virtuele- virtuele machine te maken.

1. Selecteer **rdp-bestand downloaden**op de pagina Verbinding **maken met RDP** . In Azure wordt een *RDP*-bestand (Remote Desktop Protocol) gemaakt en het bestand wordt gedownload naar de computer.

1. Open het gedownloade *RDP*-bestand. Selecteer **Verbinding maken** wanneer hierom wordt gevraagd. Selecteer **Meer opties** > **Gebruik een ander account**en voer vervolgens de gebruikersnaam en het wachtwoord in die u hebt opgegeven bij het maken van de privé-virtuele-vm.

1. Selecteer **OK**.

1. Als u tijdens het aanmeldingsproces een certificaatwaarschuwing ontvangt, selecteert u **Ja** om verbinding te maken met de virtuele machine.

### <a name="enable-icmp-through-the-windows-firewall"></a>ICMP via Windows Firewall inschakelen

In een latere stap gebruikt u het hulpprogramma Route traceren om de routering te testen. Route traceren maakt gebruik van ICMP (Internet Control Message Protocol), wat in Windows Firewall standaard wordt geweigerd. Schakel ICMP via Windows Firewall in.

1. Open PowerShell in het externe bureaublad van *myVmPrivate*.

1. Voer de volgende opdracht in:

    ```powershell
    New-NetFirewallRule –DisplayName "Allow ICMPv4-In" –Protocol ICMPv4
    ```

    U gebruikt trace route om routering te testen in deze zelfstudie. Voor productieomgevingen wordt aanbevolen om ICMP via Windows Firewall niet toe te staan.

### <a name="turn-on-ip-forwarding-within-myvmnva"></a>Doorsturen via IP inschakelen binnen myVmNva

U hebt [Doorsturen via IP ingeschakeld](#turn-on-ip-forwarding) voor de netwerkinterface van de VM met behulp van Azure. Netwerkverkeer moet ook worden doorgestuurd via het besturingssysteem van de VM. Doorsturen via IP inschakelen voor het besturingssysteem van de VM *myVmNva* met behulp van deze opdrachten.

1. Open vanaf een opdrachtprompt op de VM *myVmPrivate* een extern bureaublad naar de VM *myVmNva*:

    ```cmd
    mstsc /v:myvmnva
    ```

1. Voer vanuit PowerShell op de *myVmNva* VM deze opdracht in om IP-forwarding in te schakelen:

    ```powershell
    Set-ItemProperty -Path HKLM:\SYSTEM\CurrentControlSet\Services\Tcpip\Parameters -Name IpEnableRouter -Value 1
    ```

1. Start de *myVmNva* VM opnieuw: selecteer vanaf de taakbalk >  **Startkracht** > **Power**, **Overige (Gepland)****Doorgaan**.

    Hierdoor wordt ook de externe desktopsessie verbroken.

1. Maak nadat de VM *myVmNva* opnieuw is opgestart, een externe bureaubladsessie naar de VM *myVmPublic*. Houd de verbinding met de VM *myVmPrivate* in stand, open een opdrachtprompt en voer de volgende opdracht uit:

    ```cmd
    mstsc /v:myVmPublic
    ```
1. In het externe bureaublad van *myVmPublic,* open PowerShell.

1. Schakel ICMP via Windows Firewall in door de volgende opdracht in te voeren:

    ```powershell
    New-NetFirewallRule –DisplayName "Allow ICMPv4-In" –Protocol ICMPv4
    ```

## <a name="test-the-routing-of-network-traffic"></a>De routering van netwerkverkeer testen

Eerst gaan we de routering van het netwerkverkeer testen vanaf de VM *myVmPublic* naar de VM *myVmPrivate*.

1. Voer vanuit PowerShell in de VM *myVmPublic* de volgende opdracht uit:

    ```powershell
    tracert myVmPrivate
    ```

    Het antwoord is vergelijkbaar met dit voorbeeld:

    ```powershell
    Tracing route to myVmPrivate.vpgub4nqnocezhjgurw44dnxrc.bx.internal.cloudapp.net [10.0.1.4]
    over a maximum of 30 hops:

    1    <1 ms     *        1 ms  10.0.2.4
    2     1 ms     1 ms     1 ms  10.0.1.4

    Trace complete.
    ```

    U zien dat de eerste hop is tot 10.0.2.4, dat is NVA's prive IP-adres. De tweede hop is naar het privé IP-adres van de *myVmPrivate* VM: 10.0.1.4. Eerder hebt u de route toegevoegd aan de routetabel *myRouteTablePublic* en deze gekoppeld aan het *Openbare* subnet. Als gevolg hiervan is het verkeer in Azure verzonden via de NVA en niet rechtstreeks via het *Privé*subnet.

1. Sluit de externe bureaubladsessie met de VM *myVmPublic*. U houdt nog verbinding met de VM *myVmPrivate*.

1. Voer vanuit een opdrachtprompt op de VM *myVmPrivate* de volgende opdracht uit:

    ```cmd
    tracert myVmPublic
    ```

    Met deze opdracht wordt de routering van netwerkverkeer van de *myVmPrivate* VM naar de *myVmPublic* VM getest. Het antwoord is vergelijkbaar met dit voorbeeld:

    ```cmd
    Tracing route to myVmPublic.vpgub4nqnocezhjgurw44dnxrc.bx.internal.cloudapp.net [10.0.0.4]
    over a maximum of 30 hops:

    1     1 ms     1 ms     1 ms  10.0.0.4

    Trace complete.
    ```

    U zien dat Azure verkeer rechtstreeks van de *myVmPrivate* VM naar de *myVmPublic* VM leidt. Standaard routeert Azure verkeer rechtstreeks tussen subnetten.

1. Sluit de externe bureaubladsessie naar de VM *myVmPrivate*.

## <a name="clean-up-resources"></a>Resources opschonen

Wanneer de resourcegroep niet meer nodig is, verwijdert u *myResourceGroup* en alle resources die deze heeft:

1. Ga naar de [Azure-portal](https://portal.azure.com) om uw brongroep te beheren. Resourcegroepen zoeken **Resource groups**en selecteren .

1. Kies de naam van uw resourcegroep **(myResourceGroup).**

1. Selecteer **Resourcegroep verwijderen**.

1. Typ in het bevestigingsdialoogvenster *myResourceGroup* voor **TYPE DE NAAM VAN DE BRONGROEP**en selecteer **Verwijderen**. Azure verwijdert de *myResourceGroup* en alle resources die aan die brongroep zijn gekoppeld, inclusief uw routetabellen, opslagaccounts, virtuele netwerken, VM's, netwerkinterfaces en openbare IP-adressen.

## <a name="next-steps"></a>Volgende stappen

In deze zelfstudie hebt u een routetabel gemaakt en die aan een subnet gekoppeld. U hebt een eenvoudig NVA gemaakt dat verkeer van een openbaar subnet naar een privé-subnet heeft geleid. Nu u verschillende vooraf geconfigureerde NVA's implementeren vanuit de [Azure Marketplace,](https://azuremarketplace.microsoft.com/marketplace/apps/category/networking)die veel nuttige netwerkfuncties bieden. Zie [Routeringoverzicht](virtual-networks-udr-overview.md) en [Routetabel beheren](manage-route-table.md) voor meer informatie over routeren.

Hoewel u veel Azure-resources binnen een virtueel netwerk kunt implementeren, kunnen voor sommige Azure PaaS-services met Azure geen resources in een virtueel netwerk worden geïmplementeerd. Het is mogelijk om de toegang tot de bronnen van sommige Azure PaaS-services te beperken, hoewel de beperking alleen verkeer van een virtueel netwerksubnet moet zijn. Zie de volgende zelfstudie voor meer informatie over het beperken van netwerktoegang tot Azure PaaS-bronnen.

> [!div class="nextstepaction"]
> [Netwerktoegang tot PaaS-resources beperken](tutorial-restrict-network-access-to-resources.md)
