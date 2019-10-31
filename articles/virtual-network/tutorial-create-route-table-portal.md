---
title: Netwerkverkeer routeren - zelfstudie - Azure-portal
titlesuffix: Azure Virtual Network
description: In deze zelfstudie leert u netwerkverkeer te routeren met een routetabel met behulp van Azure Portal.
services: virtual-network
documentationcenter: virtual-network
author: KumudD
manager: twooley
editor: ''
tags: azure-resource-manager
Customer intent: I want to route traffic from one subnet, to a different subnet, through a network virtual appliance.
ms.assetid: ''
ms.service: virtual-network
ms.devlang: azurecli
ms.topic: tutorial
ms.tgt_pltfrm: virtual-network
ms.workload: infrastructure
ms.date: 12/12/2018
ms.author: kumud
ms.custom: mvc
ms.openlocfilehash: 7e7a01b7fdc1a508fa19397900f8fd4f52d49c53
ms.sourcegitcommit: 0b1a4101d575e28af0f0d161852b57d82c9b2a7e
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 10/30/2019
ms.locfileid: "73164000"
---
# <a name="tutorial-route-network-traffic-with-a-route-table-using-the-azure-portal"></a>Zelfstudie: Netwerkverkeer routeren met een routetabel met behulp van Azure Portal

In Azure wordt verkeer standaard geretourneerd tussen alle subnetten in een virtueel netwerk. U kunt uw eigen routes maken om de standaardroutering van Azure te overschrijven. De mogelijkheid voor het maken van aangepaste routes is handig als u bijvoorbeeld verkeer tussen subnetten wilt routeren via een NVA (virtueel netwerkapparaat). In deze zelfstudie leert u het volgende:

> [!div class="checklist"]
> * Een routetabel maken
> * Een route maken
> * Een virtueel netwerk met meerdere subnetten maken
> * Een routetabel aan een subnet koppelen
> * Een NVA maken voor het routeren van verkeer
> * Virtuele machines (VM's) implementeren in verschillende subnetten
> * Verkeer van het ene subnet naar het andere leiden via een NVA

U kunt deze zelfstudie desgewenst voltooien met behulp van [Azure CLI](tutorial-create-route-table-cli.md) of [Azure PowerShell](tutorial-create-route-table-powershell.md).

Als u nog geen abonnement op Azure hebt, maak dan een [gratis account](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) aan voordat u begint.

## <a name="sign-in-to-azure"></a>Aanmelden bij Azure

Meld u aan bij de [Azure-portal](https://portal.azure.com).

## <a name="create-a-route-table"></a>Een routetabel maken

1. Selecteer linksboven in het scherm **Een resource maken** > **Netwerken** > **Routetabel**.

1. Typ of selecteer in **Routetabel maken** de volgende gegevens:

    | Instelling | Waarde |
    | ------- | ----- |
    | Naam | Voer *myRouteTablePublic* in. |
    | Abonnement | Selecteer uw abonnement. |
    | Resourcegroep | Selecteer **Nieuwe maken**, voer *myResourceGroup* in en selecteer *OK*. |
    | Locatie | Laat de standaardwaarde **US - oost** staan.
    | Doorgifte van BGP-route | Laat de standaardwaarde **Ingeschakeld** staan. |

1. Selecteer **Maken**.

## <a name="create-a-route"></a>Een route maken

1. Voer in de zoekbalk van de portal *myRouteTablePublic* in.

1. Wanneer **myRouteTablePublic** wordt weergegeven in de zoekresultaten, selecteert u dit.

1. Selecteer in **myRouteTablePublic** onder **Instellingen** de optie **Routes** >  **+ Toevoegen**.

    ![Route toevoegen](./media/tutorial-create-route-table-portal/add-route.png)

1. Typ of selecteer in **Route toevoegen** de volgende gegevens:

    | Instelling | Waarde |
    | ------- | ----- |
    | Routenaam | Voer *ToPrivateSubnet* in. |
    | Adresvoorvoegsel | Voer *10.0.1.0/24* in. |
    | Volgend hoptype | Selecteer **Virtueel apparaat**. |
    | Adres van de volgende hop | Voer *10.0.2.4* in. |

1. Selecteer **OK**.

## <a name="associate-a-route-table-to-a-subnet"></a>Een routetabel aan een subnet koppelen

Voordat u een routetabel aan een subnet kunt koppelen, moet u een virtueel netwerk en subnet maken.

### <a name="create-a-virtual-network"></a>Maak een virtueel netwerk

1. Selecteer linksboven in het scherm **Een resource maken** > **Netwerken** > **Virtueel netwerk**.

1. Typ of selecteer in **Virtueel netwerk maken** de volgende gegevens:

    | Instelling | Waarde |
    | ------- | ----- |
    | Naam | Voer *myVirtualNetwork* in. |
    | Adresruimte | Voer *10.0.0.0/16* in. |
    | Abonnement | Selecteer uw abonnement. |
    | Resourcegroep | Selecteer ***Bestaande selecteren*** > **myResourceGroup**. |
    | Locatie | Laat de standaardwaarde **US - oost** staan. |
    | Subnet - naam | Voer *Openbaar* in. |
    | Subnet - adresbereik | Voer *10.0.0.0/24* in. |

1. Laat de overige standaardwaarden staan en selecteer **Maken**.

### <a name="add-subnets-to-the-virtual-network"></a>Subnetten toevoegen aan het virtuele netwerk

1. Voer in de zoekbalk van de portal *myVirtualNetwork* in.

1. Wanneer **myVirtualNetwork** wordt weergegeven in de zoekresultaten, selecteert u dit.

1. Selecteer in **myVirtualNetwork** onder **Instellingen** de optie **Subnetten** >  **+ Subnet**.

    ![Subnet toevoegen](./media/tutorial-create-route-table-portal/add-subnet.png)

1. Voer in **Subnet toevoegen** de volgende gegevens in:

    | Instelling | Waarde |
    | ------- | ----- |
    | Naam | Voer *Privé* in. |
    | Adresruimte | Voer *10.0.1.0/24* in. |

1. Laat de overige standaardwaarden staan en selecteer **OK**.

1. Selecteer **+ Subnet** opnieuw. Voer ditmaal de volgende gegevens in:

    | Instelling | Waarde |
    | ------- | ----- |
    | Naam | Voer *DMZ* in. |
    | Adresruimte | Voer *10.0.2.0/24* in. |

1. Laat net zoals de vorige keer de standaardwaarden staan en selecteer **OK**.

    Azure toont de drie subnetten: **openbaar**, **privé**en **DMZ**.

### <a name="associate-myroutetablepublic-to-your-public-subnet"></a>MyRouteTablePublic koppelen aan het openbare subnet

1. Selecteer **Openbaar**.

1. Selecteer in **Openbaar** de optie **Routetabel** > **MyRouteTablePublic** > **Opslaan**.

    ![Routetabel koppelen](./media/tutorial-create-route-table-portal/associate-route-table.png)

## <a name="create-an-nva"></a>Een NVA maken

NVA’s zijn VM’s die helpen met netwerkfuncties zoals routering en firewalloptimalisatie. Als u wilt, kunt u een ander besturingssysteem selecteren. In deze zelfstudie wordt ervan uitgegaan dat u **Windows Server 2016 Datacenter** gebruikt.

1. Selecteer **Een resource maken** > **Compute** > **Windows Server 2016 Datacenter** linksboven in het scherm.

1. Typ of selecteer in **Een virtuele machine maken - Basisprincipes** de volgende gegevens:

    | Instelling | Waarde |
    | ------- | ----- |
    | **PROJECTGEGEVENS** | |
    | Abonnement | Selecteer uw abonnement. |
    | Resourcegroep | Selecteer **myResourceGroup**. |
    | **INSTANTIEDETAILS** |  |
    | Naam van de virtuele machine | Voer *myVmNva* in. |
    | Regio | Selecteer **US - oost**. |
    | Beschikbaarheidsopties | Laat de standaardwaarde **Geen infrastructuurredundantie vereist** staan. |
    | afbeelding | Laat de standaardwaarde **Windows Server 2016 Datacenter** staan. |
    | Grootte | Laat de standaardwaarde **Standard DS1 v2** staan. |
    | **ADMINISTRATOR-ACCOUNT** |  |
    | Gebruikersnaam | Voer een gebruikersnaam naar keuze in. |
    | Wachtwoord | Voer een wachtwoord naar keuze in. Het wachtwoord moet minstens 12 tekens lang zijn en moet voldoen aan de [gedefinieerde complexiteitsvereisten](../virtual-machines/windows/faq.md?toc=%2fazure%2fvirtual-network%2ftoc.json#what-are-the-password-requirements-when-creating-a-vm).|
    | Wachtwoord bevestigen | Voer het wachtwoord opnieuw in. |
    | **REGELS VOOR BINNENKOMENDE POORT** |  |
    | Openbare poorten voor inkomend verkeer | Laat de standaardwaarde **Geen** staan.
    | **GELD BESPAREN** |  |
    | Hebt u al een Windows-licentie? | Laat de standaardwaarde **Nee** staan. |

1. Selecteer **volgende: schijven**.

1. Selecteer in **Een virtuele machine maken - Schijven** de instellingen die voldoen aan uw behoeften.

1. Selecteer **volgende: netwerken**.

1. Selecteer in **Een virtuele machine maken - Netwerken** de volgende gegevens:

    | Instelling | Waarde |
    | ------- | ----- |
    | Virtueel netwerk | Laat de standaardwaarde **myVirtualNetwork** staan. |
    | Subnet | Selecteer **DMZ (10.0.2.0/24)** . |
    | Openbare IP | Selecteer **Geen**. U hebt geen openbaar IP-adres nodig. De VM kan geen verbinding maken via internet.|

1. Laat de overige standaard waarden ongewijzigd en selecteer **volgende: beheer**.

1. Selecteer in **Een virtuele machine maken - Beheer** voor **Opslagaccount voor diagnose** de optie **Nieuw**.

1. Typ of selecteer in **Opslagaccount maken** de volgende gegevens:

    | Instelling | Waarde |
    | ------- | ----- |
    | Naam | Voer *mynvastorageaccount* in. |
    | Soort account | Laat de standaardwaarde **Storage (algemeen gebruik v1)** staan. |
    | Prestaties | Laat de standaardwaarde **Standard** staan. |
    | Replicatie | Laat de standaardwaarde **Lokaal redundante opslag (LRS)** staan.

1. Selecteer **OK**

1. Selecteer **Controleren + maken**. De pagina **Beoordelen en maken** wordt weergegeven en de configuratie wordt gevalideerd in Azure.

1. Wanneer u de tekst **Validatie voltooid** ziet, selecteert u **Maken**.

    Het maken van de virtuele machine duurt een paar minuten. Ga pas verder als het maken van de VM in Azure is voltooid. Op de pagina **Uw implementatie wordt uitgevoerd** worden de details van de implementatie weergegeven.

1. Als de VM klaar is, selecteert u **Ga naar resource**.

## <a name="turn-on-ip-forwarding"></a>Doorsturen via IP inschakelen

Doorsturen via IP inschakelen voor *myVmNva*. Wanneer u in Azure netwerkverkeer verzendt naar *myVmNva*, wordt dit verkeer, als het is bestemd voor een ander IP-adres, op basis van Doorsturen via IP naar de juiste locatie verzonden.

1. Selecteer in **myVmNva** onder **Instellingen** de optie **Netwerken**.

1. Selecteer **myvmnva123**. Dit is de netwerkinterface die in Azure is gemaakt voor uw VM. Deze heeft een tekenreeks met cijfers die de interface uniek maken voor u.

    ![VM-netwerken](./media/tutorial-create-route-table-portal/virtual-machine-networking.png)

1. Selecteer onder **Instellingen** de optie **IP-configuraties**.

1. Selecteer in **myvmnva123 - IP-configuraties** voor **Doorsturen via IP** de optie **Ingeschakeld** en selecteer vervolgens **Opslaan**.

    ![Doorsturen via IP inschakelen](./media/tutorial-create-route-table-portal/enable-ip-forwarding.png)

## <a name="create-public-and-private-virtual-machines"></a>Virtuele machines maken, openbaar en privé

Maak een openbare VM en een privé-VM in het virtuele netwerk. Later gebruikt u deze om te zien dat het *Openbare* subnetverkeer in Azure via de NVA naar het *Privé*subnet wordt gerouteerd.

Voltooi stap 1-12 van [Een VM maken](#create-an-nva). Gebruik dezelfde instellingen. Deze waarden zijn de waarden die moeten worden gewijzigd:

| Instelling | Waarde |
| ------- | ----- |
| **OPENBARE VM** | |
| BASISPRINCIPES |  |
| Naam van de virtuele machine | Voer *myVmPublic* in. |
| NETWERKEN | |
| Subnet | Selecteer **Openbaar (10.0.0.0/24)** . |
| Openbaar IP-adres | Accepteer de standaardwaarde. |
| Openbare poorten voor inkomend verkeer | Selecteer **Geselecteerde poorten toestaan**. |
| Binnenkomende poorten selecteren | Selecteer **HTTP** en **RDP**. |
| BEHEER | |
| Opslagaccount voor diagnose | Laat de standaardwaarde **mynvastorageaccount** staan. |
| **PRIVÉ-VM** | |
| BASISPRINCIPES |  |
| Naam van de virtuele machine | Voer *myVmPrivate* in. |
| NETWERKEN | |
| Subnet | Selecteer **Privé (10.0.1.0/24)** . |
| Openbaar IP-adres | Accepteer de standaardwaarde. |
| Openbare poorten voor inkomend verkeer | Selecteer **Geselecteerde poorten toestaan**. |
| Binnenkomende poorten selecteren | Selecteer **HTTP** en **RDP**. |
| BEHEER | |
| Opslagaccount voor diagnose | Laat de standaardwaarde **mynvastorageaccount** staan. |

U kunt de VM *myVmPrivate* maken terwijl Azure de VM *myVmPublic* maakt. Ga pas verder met de rest van de stappen als het maken van beide VM’s is voltooid in Azure.

## <a name="route-traffic-through-an-nva"></a>Verkeer routeren via een NVA

### <a name="sign-in-to-myvmprivate-over-remote-desktop"></a>Aanmelden bij myVmPrivate via het externe bureaublad

1. Voer in de zoekbalk van de portal *myVmPrivate* in.

1. Wanneer de VM **myVmPrivate** wordt weergegeven in zoekresultaten, selecteert u deze.

1. Selecteer **Verbinding maken** om een extern bureaubladverbinding te maken met de VM *myVmPrivate*.

1. Selecteer in **Verbinding maken met virtuele machine** de optie **RDP-bestand downloaden**. Azure maakt een Remote Desktop Protocol-bestand ( *.rdp*) en downloadt het bestand naar uw computer.

1. Open het gedownloade *RDP*-bestand.

    1. Selecteer **Verbinding maken** wanneer hierom wordt gevraagd.

    1. Voer de gebruikersnaam en het wachtwoord in die u hebt opgegeven bij het maken van de privé-VM.

    1. Mogelijk moet u **Meer keuzen** > **Een ander account gebruiken** om de referenties van de privé-VM te gebruiken.

1. Selecteer **OK**.

    Er wordt mogelijk een certificaatwaarschuwing weergegeven tijdens het aanmelden.

1. Selecteer **Ja** om verbinding te maken met de VM.

### <a name="enable-icmp-through-the-windows-firewall"></a>ICMP via Windows Firewall inschakelen

In een latere stap gebruikt u het hulpprogramma Route traceren om de routering te testen. Route traceren maakt gebruik van ICMP (Internet Control Message Protocol), wat in Windows Firewall standaard wordt geweigerd. Schakel ICMP via Windows Firewall in.

1. Open PowerShell in het externe bureaublad van *myVmPrivate*.

1. Voer de volgende opdracht in:

    ```powershell
    New-NetFirewallRule –DisplayName "Allow ICMPv4-In" –Protocol ICMPv4
    ```

    In deze zelfstudie gebruikt u Route tracering om de routering te testen. Voor productieomgevingen wordt aanbevolen om ICMP via Windows Firewall niet toe te staan.

### <a name="turn-on-ip-forwarding-within-myvmnva"></a>Doorsturen via IP inschakelen binnen myVmNva

U hebt [Doorsturen via IP ingeschakeld](#turn-on-ip-forwarding) voor de netwerkinterface van de VM met behulp van Azure. Netwerkverkeer moet ook worden doorgestuurd via het besturingssysteem van de VM. Doorsturen via IP inschakelen voor het besturingssysteem van de VM *myVmNva* met behulp van deze opdrachten.

1. Open vanaf een opdrachtprompt op de VM *myVmPrivate* een extern bureaublad naar de VM *myVmNva*:

    ```cmd
    mstsc /v:myvmnva
    ```

1. Voer vanuit PowerShell inde *myVmNva* deze opdracht in om Doorsturen via IP in te schakelen:

    ```powershell
    Set-ItemProperty -Path HKLM:\SYSTEM\CurrentControlSet\Services\Tcpip\Parameters -Name IpEnableRouter -Value 1
    ```

1. Start de VM *myVmNva* opnieuw op. Selecteer in de taakbalk **Knop Start** > **Aan/uit-knop**, **Overige (gepland)**  > **Doorgaan**.

    Hierdoor wordt de externe bureaubladsessie ook verbroken.

1. Maak nadat de VM *myVmNva* opnieuw is opgestart, een externe bureaubladsessie naar de VM *myVmPublic*. Houd de verbinding met de VM *myVmPrivate* in stand, open een opdrachtprompt en voer de volgende opdracht uit:

    ```cmd
    mstsc /v:myVmPublic
    ```
1. Open PowerShell in het externe bureaublad van *myVmPublic*.

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

    U ziet dat de eerste hop naar 10.0.2.4 is. Het is het privé-IP-adres van NVA. De tweede hop is het privé-IP-adres van de *VM myvmprivate* VM: 10.0.1.4. Eerder hebt u de route toegevoegd aan de routetabel *myRouteTablePublic* en deze gekoppeld aan het *Openbare* subnet. Als gevolg hiervan is het verkeer in Azure verzonden via de NVA en niet rechtstreeks via het *Privé*subnet.

1. Sluit de externe bureaubladsessie met de VM *myVmPublic*. U houdt nog verbinding met de VM *myVmPrivate*.

1. Voer vanuit een opdrachtprompt op de VM *myVmPrivate* de volgende opdracht uit:

    ```cmd
    tracert myVmPublic
    ```

    Hierdoor wordt de routering van het netwerkverkeer getest vanaf de VM *myVmPrivate* naar de VM *myVmPublic*. Het antwoord is vergelijkbaar met dit voorbeeld:

    ```cmd
    Tracing route to myVmPublic.vpgub4nqnocezhjgurw44dnxrc.bx.internal.cloudapp.net [10.0.0.4]
    over a maximum of 30 hops:

    1     1 ms     1 ms     1 ms  10.0.0.4

    Trace complete.
    ```

    U ziet dat verkeer in Azure rechtstreeks vanuit *myVmPrivate* naar *myVmPublic* wordt geleid. Standaard routeert Azure verkeer rechtstreeks tussen subnetten.

1. Sluit de externe bureaubladsessie naar de VM *myVmPrivate*.

## <a name="clean-up-resources"></a>Resources opschonen

Wanneer u de resourcegroep niet meer nodig hebt, verwijdert u deze en alle bijbehorende resources:

1. Voer in de zoekbalk van de portal *myResourceGroup* in.

1. Wanneer u **myResourceGroup** ziet in de zoekresultaten, selecteert u deze.

1. Selecteer **Resourcegroep verwijderen**.

1. Voer *myResourceGroup* in voor **TYP DE RESOURCEGROEPNAAM:** en selecteer **Verwijderen**.

## <a name="next-steps"></a>Volgende stappen

In deze zelfstudie hebt u een routetabel gemaakt en die aan een subnet gekoppeld. U hebt een eenvoudig NVA gemaakt dat verkeer van een openbaar subnet naar een privé-subnet heeft geleid. Nu u dit allemaal weet, kunt u verschillende vooraf geconfigureerde NVA’s implementeren vanuit [Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/apps/category/networking). Met deze NVA’s kunnen veel handige netwerkfuncties worden uitgevoerd. Zie [Routeringoverzicht](virtual-networks-udr-overview.md) en [Routetabel beheren](manage-route-table.md) voor meer informatie over routeren.

Hoewel u veel Azure-resources binnen een virtueel netwerk kunt implementeren, kunnen voor sommige Azure PaaS-services met Azure geen resources in een virtueel netwerk worden geïmplementeerd. U kunt de toegang tot resources van sommige Azure PaaS-services beperken. De beperking mag echter alleen verkeer van een netwerksubnet zijn. Ga verder met de volgende zelfstudie als u wilt leren hoe u de netwerktoegang kunt beperken tot Azure PaaS-resources.

> [!div class="nextstepaction"]
> [Netwerktoegang tot PaaS-resources beperken](tutorial-restrict-network-access-to-resources.md)
