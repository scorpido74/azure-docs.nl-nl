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
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/29/2020
ms.locfileid: "81314292"
---
# <a name="tutorial-route-network-traffic-with-a-route-table-using-the-azure-portal"></a>Zelfstudie: Netwerkverkeer routeren met een routetabel met behulp van Azure Portal

In Azure wordt verkeer standaard geretourneerd tussen alle subnetten in een virtueel netwerk. U kunt uw eigen routes maken om de standaardroutering van Azure te overschrijven. Aangepaste routes zijn handig wanneer u bijvoorbeeld verkeer tussen subnetten wilt routeren via een virtueel netwerk apparaat (NVA). In deze zelfstudie leert u het volgende:

> [!div class="checklist"]
> * Een NVA maken voor het routeren van verkeer
> * Een routetabel maken
> * Een route maken
> * Een routetabel aan een subnet koppelen
> * Virtuele machines (VM's) implementeren in verschillende subnetten
> * Verkeer van het ene subnet naar het andere leiden via een NVA

In deze zelf studie wordt gebruikgemaakt van de [Azure Portal](https://portal.azure.com). U kunt ook [Azure cli](tutorial-create-route-table-cli.md) of [Azure PowerShell](tutorial-create-route-table-powershell.md)gebruiken.

Als u nog geen abonnement op Azure hebt, maak dan een [gratis account](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) aan voordat u begint.

## <a name="create-an-nva"></a>Een NVA maken

Virtuele netwerk apparaten (Nva's) zijn virtuele machines die ondersteuning bieden voor netwerk functies, zoals route ring en firewall optimalisatie. In deze zelfstudie wordt ervan uitgegaan dat u **Windows Server 2016 Datacenter** gebruikt. Als u wilt, kunt u een ander besturingssysteem selecteren.

1. Selecteer in het menu [Azure Portal](https://portal.azure.com) of op de **Start** pagina de optie **een resource maken**.

1. Kies **beveiliging** > **Windows Server 2016 Data Center**.

    ![Windows Server 2016 Data Center, een VM maken, Azure Portal](./media/tutorial-create-route-table-portal/vm-ws2016-datacenter.png)

1. Voer op de pagina **een virtuele machine maken** onder **basis**van de volgende informatie in of Selecteer deze:

    | Sectie | Instelling | Bewerking |
    | ------- | ------- | ----- |
    | **Project Details** | Abonnement | Kies uw abonnement. |
    | | Resourcegroep | Selecteer **Nieuwe maken**, voer *myResourceGroup* in en selecteer **OK**. |
    | **Exemplaar Details** | Naam van de virtuele machine | Voer *myVmNva* in. |
    | | Regio | Kies **(VS) vs-Oost**. |
    | | Beschikbaarheidsopties | Kies **geen infra structuur-redundantie vereist**. |
    | | Installatiekopie | Kies **Windows Server 2016 Data Center**. |
    | | Grootte | Behoud de standaard, **standaard ds1 v2**. |
    | **Administrator-account** | Gebruikersnaam | Voer een gebruikersnaam naar keuze in. |
    | | Wachtwoord | Voer een wacht woord in dat ten minste twaalf tekens moet bevatten en aan de [gedefinieerde complexiteits vereisten](../virtual-machines/windows/faq.md?toc=%2fazure%2fvirtual-network%2ftoc.json#what-are-the-password-requirements-when-creating-a-vm)voldoet. |
    | | Wachtwoord bevestigen | Voer het wacht woord opnieuw in. |
    | **Binnenkomende poort regels** | Openbare poorten voor inkomend verkeer | Kies **geen**. |
    | **Geld besparen** | Hebt u al een Windows Server-licentie? | Kies **Nee**. |

    ![Basis beginselen, een virtuele machine maken Azure Portal](./media/tutorial-create-route-table-portal/basics-create-virtual-machine.png)

    Selecteer vervolgens **volgende: schijven >**.

1. Onder **schijven**selecteert u de instellingen die geschikt zijn voor uw behoeften en selecteert u **volgende: netwerk >**.

1. Onder **netwerken**:

    1. Selecteer **nieuwe maken**voor het **virtuele netwerk**.
    
    1. Voer in het dialoog venster **virtueel netwerk maken** onder **naam** *myVirtualNetwork*in.

    1. Vervang in **adres ruimte**het bestaande adres bereik door *10.0.0.0/16*.

    1. Selecteer in **subnetten**het **Verwijder** pictogram om het bestaande subnet te verwijderen en voer vervolgens de volgende combi Naties van **subnet naam** en **adres bereik**in. Wanneer u een geldige naam en een geldig bereik hebt opgegeven, wordt er een nieuwe lege rij onder deze weer gegeven.

        | Subnetnaam | Adresbereik |
        | ----------- | ------------- |
        | *Public* | *10.0.0.0/24* |
        | *Privé* | *10.0.1.0/24* |
        | *DMZ* | *10.0.2.0/24* |

    1. Selecteer **OK** om het dialoog venster te sluiten.

    1. Kies in **subnet** **DMZ (10.0.2.0/24)**.

    1. Kies in **openbaar IP-adres** **geen**, omdat deze VM geen verbinding kan maken via internet.

    1. Selecteer **volgende: beheer >**.

1. Onder **beheer**:

    1. Selecteer **nieuwe maken**in het **opslag account voor diagnostische gegevens**.
    
    1. Voer in het dialoog venster **opslag account maken** de volgende gegevens in of Selecteer deze:

        | Instelling | Waarde |
        | ------- | ----- |
        | Naam | *mynvastorageaccount* |
        | Soort account | **Opslag (algemeen gebruik v1)** |
        | Prestaties | **Standaard** |
        | Replicatie | **Lokaal redundante opslag (LRS)** |
    
    1. Selecteer **OK** om het dialoog venster te sluiten.

    1. Selecteer **controleren + maken**. U gaat naar de pagina **controleren en maken** en Azure valideert uw configuratie.

1. Wanneer u het bericht **door gegeven validatie** ziet, selecteert u **maken**.

    Het maken van de virtuele machine duurt een paar minuten. Wacht totdat Azure klaar is met het maken van de VM. Op de pagina **uw implementatie** vindt u informatie over de implementatie.

1. Als de VM klaar is, selecteert u **Ga naar resource**.

## <a name="create-a-route-table"></a>Een routetabel maken

1. Selecteer in het menu [Azure Portal](https://portal.azure.com) of op de **Start** pagina de optie **een resource maken**.

2. Voer in het zoekvak *route tabel*in. Wanneer **route tabel** wordt weer gegeven in de zoek resultaten, selecteert u deze.

3. Selecteer op de pagina **route tabel** de optie **maken**.

4. Typ of selecteer in **Routetabel maken** de volgende gegevens:

    | Instelling | Waarde |
    | ------- | ----- |
    | Naam | *myRouteTablePublic* |
    | Abonnement | Uw abonnement |
    | Resourcegroep | **myResourceGroup** |
    | Locatie | **VS VS-Oost** |
    | Route doorgifte van virtuele netwerk gateway | **Ingeschakeld** |

    ![Route tabel maken, Azure Portal](./media/tutorial-create-route-table-portal/create-route-table.png)

5. Selecteer **Maken**.

## <a name="create-a-route"></a>Een route maken

1. Ga naar de [Azure Portal](https://portal.azure.com) om uw route tabel te beheren. Zoek en selecteer **route tabellen**.

1. Kies de naam van uw route tabel (**myRouteTablePublic**).

1. Kies **routes** > **toevoegen**.

    ![Route toevoegen, route tabel, Azure Portal](./media/tutorial-create-route-table-portal/add-route.png)

1. Typ of selecteer in **Route toevoegen** de volgende gegevens:

    | Instelling | Waarde |
    | ------- | ----- |
    | Routenaam | *ToPrivateSubnet* |
    | Adresvoorvoegsel | *10.0.1.0/24* (het adres bereik van het *privé* subnet dat eerder is gemaakt) |
    | Volgend hoptype | **Virtueel apparaat** |
    | Adres van de volgende hop | *10.0.2.4* (een adres binnen het adres bereik van het subnet *DMZ* ) |

1. Selecteer **OK**.

## <a name="associate-a-route-table-to-a-subnet"></a>Een routetabel aan een subnet koppelen

1. Ga naar de [Azure Portal](https://portal.azure.com) om uw virtuele netwerk te beheren. Zoek en selecteer **virtuele netwerken**.

1. Kies de naam van uw virtuele netwerk (**myVirtualNetwork**).

1. Kies **subnetten**in de menu balk van het virtuele netwerk.

1. Kies in de lijst subnet van het virtuele netwerk de optie **openbaar**.

1. Kies in **route tabel**de route tabel die u hebt gemaakt (**myRouteTablePublic**) en selecteer vervolgens **Opslaan** om uw route tabel te koppelen aan het *open bare* subnet.

    ![De route tabel, de subnetlijst, het virtuele netwerk, het Azure Portal koppelen](./media/tutorial-create-route-table-portal/associate-route-table.png)

## <a name="turn-on-ip-forwarding"></a>Doorsturen via IP inschakelen

Schakel vervolgens door sturen via IP in voor uw nieuwe virtuele NVA-machine, *VM myvmnva*. Wanneer Azure netwerk verkeer naar *VM myvmnva*verzendt en het verkeer bestemd is voor een ander IP-adres, verzendt door sturen via IP het verkeer naar de juiste locatie.

1. Ga naar de [Azure Portal](https://portal.azure.com) om uw VM te beheren. Zoek en selecteer **virtuele machines**.

1. Kies de naam van uw virtuele machine (**VM myvmnva**).

1. Selecteer in de menu balk van de virtuele NVA-machine **netwerken**.

1. Selecteer **myvmnva123**. Dit is de netwerkinterface die in Azure is gemaakt voor uw VM. Azure voegt nummers toe om een unieke naam te garanderen.

    ![Netwerken, virtuele machine (virtuele netwerk apparaten) (NVA), Azure Portal](./media/tutorial-create-route-table-portal/virtual-machine-networking.png)

1. Selecteer **IP-configuraties**in de menu balk van de netwerk interface.

1. Op de pagina **IP-configuraties** stelt u **door sturen via IP** in op **ingeschakeld**en selecteert u **Opslaan**.

    ![Door sturen via IP inschakelen, IP-configuraties, netwerk interface, virtueel netwerk apparaat (NVA) virtuele machine (VM), Azure Portal](./media/tutorial-create-route-table-portal/enable-ip-forwarding.png)

## <a name="create-public-and-private-virtual-machines"></a>Virtuele machines maken, openbaar en privé

Maak een openbare VM en een privé-VM in het virtuele netwerk. Later gebruikt u deze om te zien dat het *Openbare* subnetverkeer in Azure via de NVA naar het *Privé*subnet wordt gerouteerd.

Volg de stappen voor het [maken van een NVA](#create-an-nva) om de open bare virtuele machine en de privécloud te maken. U hoeft niet te wachten tot de implementatie is voltooid of naar de VM-resource te gaan. U gebruikt de meeste instellingen, behalve zoals hieronder wordt beschreven.

Voordat u **maken** selecteert om de open bare of persoonlijke VM te maken, gaat u naar de volgende twee subsecties ([open bare VM](#public-vm) en [privécloud](#private-vm)), waarin de waarden worden weer gegeven die verschillend moeten zijn. U kunt door gaan met de volgende sectie ([verkeer routeren via een NVA](#route-traffic-through-an-nva)) nadat Azure is voltooid met het implementeren van virtuele machines.

### <a name="public-vm"></a>Open bare VM

| Tab | Instelling | Waarde |
| --- | ------- | ----- |
| Basisbeginselen | Resourcegroep | **myResourceGroup** |
| | Naam van de virtuele machine | *myVmPublic* |
| | Openbare poorten voor inkomend verkeer | **Geselecteerde poorten toestaan** |
| | Binnenkomende poorten selecteren | **RDP** |
| Netwerken | Virtueel netwerk | **myVirtualNetwork** |
| | Subnet | **Openbaar (10.0.0.0/24)** |
| | Openbaar IP-adres | De standaard instelling |
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
| | Openbaar IP-adres | De standaard instelling |
| Beheer | Opslagaccount voor diagnose | **mynvastorageaccount** |

## <a name="route-traffic-through-an-nva"></a>Verkeer routeren via een NVA

### <a name="sign-in-to-myvmprivate-over-remote-desktop"></a>Aanmelden bij myVmPrivate via het externe bureaublad

1. Ga naar de [Azure Portal](https://portal.azure.com) om uw persoonlijke VM te beheren. Zoek en selecteer **virtuele machines**.

1. Kies de naam van uw privé-VM (**VM myvmprivate**).

1. Selecteer in de VM-menu balk **verbinding maken** om een verbinding met een extern bureau blad te maken met de privécloud.

1. Selecteer op de pagina **verbinding maken met RDP** de optie **RDP-bestand downloaden**. In Azure wordt een *RDP*-bestand (Remote Desktop Protocol) gemaakt en het bestand wordt gedownload naar de computer.

1. Open het gedownloade *RDP*-bestand. Selecteer **Verbinding maken** wanneer hierom wordt gevraagd. Selecteer **meer opties** > **een ander account gebruiken**en voer vervolgens de gebruikers naam en het wacht woord in die u hebt opgegeven bij het maken van de persoonlijke VM.

1. Selecteer **OK**.

1. Als u een certificaat waarschuwing ontvangt tijdens het aanmeldings proces, selecteert u **Ja** om verbinding te maken met de virtuele machine.

### <a name="enable-icmp-through-the-windows-firewall"></a>ICMP via Windows Firewall inschakelen

In een latere stap gebruikt u het hulpprogramma Route traceren om de routering te testen. Route traceren maakt gebruik van ICMP (Internet Control Message Protocol), wat in Windows Firewall standaard wordt geweigerd. Schakel ICMP via Windows Firewall in.

1. Open PowerShell in het externe bureaublad van *myVmPrivate*.

1. Voer de volgende opdracht in:

    ```powershell
    New-NetFirewallRule –DisplayName "Allow ICMPv4-In" –Protocol ICMPv4
    ```

    In deze zelf studie gebruikt u tracerings route om de route ring te testen. Voor productieomgevingen wordt aanbevolen om ICMP via Windows Firewall niet toe te staan.

### <a name="turn-on-ip-forwarding-within-myvmnva"></a>Doorsturen via IP inschakelen binnen myVmNva

U hebt [Doorsturen via IP ingeschakeld](#turn-on-ip-forwarding) voor de netwerkinterface van de VM met behulp van Azure. Netwerkverkeer moet ook worden doorgestuurd via het besturingssysteem van de VM. Doorsturen via IP inschakelen voor het besturingssysteem van de VM *myVmNva* met behulp van deze opdrachten.

1. Open vanaf een opdrachtprompt op de VM *myVmPrivate* een extern bureaublad naar de VM *myVmNva*:

    ```cmd
    mstsc /v:myvmnva
    ```

1. Voer in Power shell op de *VM myvmnva* -VM de volgende opdracht in om door sturen via IP in te scha kelen:

    ```powershell
    Set-ItemProperty -Path HKLM:\SYSTEM\CurrentControlSet\Services\Tcpip\Parameters -Name IpEnableRouter -Value 1
    ```

1. Start de *VM myvmnva* -VM opnieuw: Klik in de taak balk op **Start** > **Power**, **Overig (gepland)** > **door gaan**.

    Hiermee wordt ook de verbinding met de extern bureau blad-sessie verbroken.

1. Maak nadat de VM *myVmNva* opnieuw is opgestart, een externe bureaubladsessie naar de VM *myVmPublic*. Houd de verbinding met de VM *myVmPrivate* in stand, open een opdrachtprompt en voer de volgende opdracht uit:

    ```cmd
    mstsc /v:myVmPublic
    ```
1. Open Power shell in het extern bureau blad van *VM myvmpublic*.

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

    U ziet dat de eerste hop 10.0.2.4 is. Dit is het privé-IP-adres van NVA. De tweede hop is het privé-IP-adres van de *VM myvmprivate* VM: 10.0.1.4. Eerder hebt u de route toegevoegd aan de routetabel *myRouteTablePublic* en deze gekoppeld aan het *Openbare* subnet. Als gevolg hiervan is het verkeer in Azure verzonden via de NVA en niet rechtstreeks via het *Privé*subnet.

1. Sluit de externe bureaubladsessie met de VM *myVmPublic*. U houdt nog verbinding met de VM *myVmPrivate*.

1. Voer vanuit een opdrachtprompt op de VM *myVmPrivate* de volgende opdracht uit:

    ```cmd
    tracert myVmPublic
    ```

    Met deze opdracht test u de route ring van het netwerk verkeer van de *VM myvmprivate* -VM naar de *VM myvmpublic* -VM. Het antwoord is vergelijkbaar met dit voorbeeld:

    ```cmd
    Tracing route to myVmPublic.vpgub4nqnocezhjgurw44dnxrc.bx.internal.cloudapp.net [10.0.0.4]
    over a maximum of 30 hops:

    1     1 ms     1 ms     1 ms  10.0.0.4

    Trace complete.
    ```

    U kunt zien dat Azure verkeer rechtstreeks van de *VM myvmprivate* -VM naar de *VM myvmpublic* -VM stuurt. Standaard routeert Azure verkeer rechtstreeks tussen subnetten.

1. Sluit de externe bureaubladsessie naar de VM *myVmPrivate*.

## <a name="clean-up-resources"></a>Resources opschonen

Als de resource groep niet meer nodig is, verwijdert u *myResourceGroup* en alle resources die het heeft:

1. Ga naar de [Azure Portal](https://portal.azure.com) om de resource groep te beheren. Zoek en selecteer **resource groepen**.

1. Kies de naam van uw resource groep (**myResourceGroup**).

1. Selecteer **Resourcegroep verwijderen**.

1. Voer in het bevestigings dialoogvenster *myResourceGroup* in bij **Typ de naam van de resource groep**en selecteer vervolgens **verwijderen**. Azure verwijdert de *myResourceGroup* en alle resources die aan de resource groep zijn gekoppeld, inclusief uw route tabellen, opslag accounts, virtuele netwerken, vm's, netwerk interfaces en open bare IP-adressen.

## <a name="next-steps"></a>Volgende stappen

In deze zelfstudie hebt u een routetabel gemaakt en die aan een subnet gekoppeld. U hebt een eenvoudig NVA gemaakt dat verkeer van een openbaar subnet naar een privé-subnet heeft geleid. U kunt nu verschillende vooraf geconfigureerde Nva's implementeren vanuit [Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/apps/category/networking), die een groot aantal nuttige netwerk functies biedt. Zie [Routeringoverzicht](virtual-networks-udr-overview.md) en [Routetabel beheren](manage-route-table.md) voor meer informatie over routeren.

Hoewel u veel Azure-resources binnen een virtueel netwerk kunt implementeren, kunnen voor sommige Azure PaaS-services met Azure geen resources in een virtueel netwerk worden geïmplementeerd. Het is mogelijk de toegang tot de resources van sommige Azure PaaS-services te beperken, maar de beperking mag alleen verkeer van een subnet van een virtueel netwerk zijn. Zie de volgende zelf studie voor meer informatie over het beperken van netwerk toegang tot Azure PaaS-resources.

> [!div class="nextstepaction"]
> [Netwerktoegang tot PaaS-resources beperken](tutorial-restrict-network-access-to-resources.md)
