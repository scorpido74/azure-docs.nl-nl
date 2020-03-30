---
title: 'ExpressRoute-versleuteling configureren: IPsec via ExpressRoute voor Azure Virtual WAN'
description: In deze zelfstudie leert u hoe u Azure Virtual WAN gebruiken om een site-to-site VPN-verbinding te maken via privépeering van ExpressRoute.
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: article
ms.date: 03/19/2020
ms.author: cherylmc
ms.openlocfilehash: b1e6305d142530ab19849f61f12a122d0c6434aa
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80059306"
---
# <a name="expressroute-encryption-ipsec-over-expressroute-for-virtual-wan"></a>ExpressRoute encryptie: IPsec over ExpressRoute voor Virtual WAN

In dit artikel ziet u hoe u Azure Virtual WAN gebruiken om een IPsec/IKE VPN-verbinding op te zetten van uw on-premises netwerk naar Azure via het privé-peering van een Azure ExpressRoute-circuit. Deze techniek kan zorgen voor een versleutelde doorvoer tussen de on-premises netwerken en Azure virtuele netwerken via ExpressRoute, zonder te gaan via het openbare internet of het gebruik van openbare IP-adressen.

## <a name="topology-and-routing"></a>Topologie en routering

In het volgende diagram ziet u een voorbeeld van VPN-connectiviteit via privépeering via ExpressRoute:

![VPN via ExpressRoute](./media/vpn-over-expressroute/vwan-vpn-over-er.png)

Het diagram toont een netwerk binnen het on-premises netwerk dat is verbonden met de Azure hub VPN-gateway via Privé-peering van ExpressRoute. De connectiviteit vestiging is eenvoudig:

1. Stel ExpressRoute-connectiviteit vast met een ExpressRoute-circuit en privé-peering.
2. Stel de VPN-connectiviteit vast zoals beschreven in dit artikel.

Een belangrijk aspect van deze configuratie is routering tussen de on-premises netwerken en Azure over zowel de ExpressRoute- als vpn-paden.

### <a name="traffic-from-on-premises-networks-to-azure"></a>Verkeer van on-premises netwerken naar Azure

Voor verkeer van on-premises netwerken naar Azure worden de Azure-voorvoegsels (inclusief de virtuele hub en alle gesproken virtuele netwerken die met de hub zijn verbonden) geadverteerd via zowel de ExpressRoute private peering BGP als de VPN BGP. Dit resulteert in twee netwerkroutes (paden) naar Azure vanuit de on-premises netwerken:

- Een over het door IPsec beschermde pad
- Direct via ExpressRoute *zonder* IPsec bescherming 

Als u versleuteling wilt toepassen op de communicatie, moet u ervoor zorgen dat voor het vpn-verbonden netwerk in het diagram de Azure-routes via on-premises VPN-gateway de voorkeur hebben boven het directe ExpressRoute-pad.

### <a name="traffic-from-azure-to-on-premises-networks"></a>Verkeer van Azure naar on-premises netwerken

Dezelfde vereiste geldt voor het verkeer van Azure naar on-premises netwerken. Om ervoor te zorgen dat het IPsec-pad de voorkeur krijgt boven het directe ExpressRoute-pad (zonder IPsec), hebt u twee opties:

- Adverteer meer specifieke voorvoegsels op de VPN BGP-sessie voor het VPN-verbonden netwerk. U adverteren voor een groter bereik dat het VPN-verbonden netwerk via ExpressRoute private peering omvat, en vervolgens meer specifieke bereiken in de VPN BGP-sessie. Adverteer bijvoorbeeld 10.0.0.0/16 via ExpressRoute en 10.0.1.0/24 via VPN.

- Adverteer onsamenhangende voorvoegsels voor VPN en ExpressRoute. Als de vpn-verbonden netwerkbereiken niet zijn verbonden ten opzichte van andere expressroute-verbonden netwerken, u de voorvoegsels adverteren in respectievelijk de VPN- en ExpressRoute BGP-sessies. Adverteer bijvoorbeeld 10.0.0.0/24 via ExpressRoute en 10.0.1.0/24 via VPN.

In beide voorbeelden stuurt Azure verkeer naar 10.0.1.0/24 via de VPN-verbinding in plaats van rechtstreeks via ExpressRoute zonder VPN-bescherming.

> [!WARNING]
> Als u *dezelfde* voorvoegsels adverteert via zowel ExpressRoute- als VPN-verbindingen, gebruikt Azure het ExpressRoute-pad rechtstreeks zonder VPN-bescherming.
>

## <a name="before-you-begin"></a>Voordat u begint

[!INCLUDE [Before you begin](../../includes/virtual-wan-tutorial-vwan-before-include.md)]

## <a name="1-create-a-virtual-wan-and-hub-with-gateways"></a><a name="openvwan"></a>1. Maak een virtuele WAN en hub met gateways

De volgende Azure-resources en de bijbehorende on-premises configuraties moeten zijn geïnstalleerd voordat u verdergaat:

- Een Azure virtual WAN
- Een virtuele WAN-hub met een [ExpressRoute-gateway](virtual-wan-expressroute-portal.md) en een [VPN-gateway](virtual-wan-site-to-site-portal.md)

Zie [Een koppeling ExpressRoute](virtual-wan-expressroute-portal.md)maken met Azure Virtual WAN voor de stappen voor het maken van een azure virtual WAN en een hub met een koppeling Met ExpressRoute. Zie [Een site-to-site-verbinding maken met Azure Virtual WAN](virtual-wan-site-to-site-portal.md)voor de stappen om een VPN-gateway in het virtuele WAN te maken.

## <a name="2-create-a-site-for-the-on-premises-network"></a><a name="site"></a>2. Een site maken voor het on-premises netwerk

De site bron is hetzelfde als de niet-ExpressRoute VPN sites voor een virtuele WAN. Het IP-adres van het on-premises VPN-apparaat kan nu een privé-IP-adres zijn, of een openbaar IP-adres in het on-premises netwerk dat bereikbaar is via ExpressRoute private peering dat in stap 1 is gemaakt.

> [!NOTE]
> Het IP-adres voor het on-premises VPN-apparaat *moet* deel uitmaken van de adresvoorvoegsels die via Azure ExpressRoute privépeering worden geadverteerd aan de virtuele WAN-hub.
>

1. Ga naar de Azure-portal in uw browser. 
1. Selecteer het WAN dat u hebt gemaakt. Selecteer op de WAN-pagina onder **Connectiviteit** **vpn-sites**.
1. Selecteer op de pagina **VPN-sites** de optie **+Site maken**.
1. Vul de volgende velden in op de pagina **Site maken**:
   * **Abonnement:** Controleer het abonnement.
   * **Resourcegroep:** selecteer of maak de resourcegroep die u wilt gebruiken.
   * **Regio:** Voer de Azure-regio in voor de VPN-sitebron.
   * **Naam:** Voer de naam in waarmee u naar uw on-premises site wilt verwijzen.
   * **Apparaatleverancier:** voer de leverancier van het on-premises VPN-apparaat in.
   * **Border Gateway Protocol:** Selecteer 'Inschakelen' als uw on-premises netwerk BGP gebruikt.
   * **Privéadresruimte:** voer de IP-adresruimte in die zich op uw on-premises locatie bevindt. Verkeer dat bestemd is voor deze adresruimte wordt via de VPN-gateway naar het on-premises netwerk doorgestuurd.
   * **Hubs**: Selecteer een of meer hubs om deze VPN-site met elkaar te verbinden. De geselecteerde hubs moeten VPN-gateways al hebben gemaakt.
1. Selecteer **Volgende: Koppelingen >** voor de VPN-koppelingsinstellingen:
   * **Linknaam:** de naam waarmee u naar deze verbinding wilt verwijzen.
   * **Naam van**de provider : De naam van de internetprovider voor deze site. Voor een on-premises netwerk van ExpressRoute is dit de naam van de ExpressRoute-serviceprovider.
   * **Snelheid**: De snelheid van de internetserviceverbinding of het ExpressRoute-circuit.
   * **IP-adres**: het openbare IP-adres van het VPN-apparaat dat zich op uw on-premises site bevindt. Of, voor ExpressRoute on-premises, het is het prive-IP-adres van het VPN-apparaat via ExpressRoute.

   Als BGP is ingeschakeld, is dit van toepassing op alle verbindingen die voor deze site in Azure zijn gemaakt. Het configureren van BGP op een virtuele WAN is gelijk aan het configureren van BGP op een Azure VPN-gateway. 
   
   Uw on-premises BGP-peeradres *mag niet* hetzelfde zijn als het IP-adres van uw VPN naar het apparaat of de virtuele netwerkadresruimte van de VPN-site. Gebruik een ander IP-adres op het VPN-apparaat voor uw IP-peer-peer. Het kan een adres zijn dat is toegewezen aan de loopback-interface op het apparaat. Het kan echter *geen* APIPA zijn (169.254.* x*. *x*) adres. Geef dit adres op in de bijbehorende lokale netwerkgateway die de locatie vertegenwoordigt. Zie [Over BGP met Azure VPN Gateway](../vpn-gateway/vpn-gateway-bgp-overview.md)voor BGP-vereisten.

1. Selecteer **Volgende: Controleren + maak >** om de instellingswaarden te controleren en de VPN-site te maken. Als u **Hubs** hebt geselecteerd om verbinding te maken, wordt de verbinding tot stand gebracht tussen het on-premises netwerk en de hub VPN-gateway.

## <a name="3-update-the-vpn-connection-setting-to-use-expressroute"></a><a name="hub"></a>3. De VPN-verbindingsinstelling bijwerken om ExpressRoute te gebruiken

Nadat u de VPN-site hebt gemaakt en verbinding hebt gemaakt met de hub, gebruikt u de volgende stappen om de verbinding te configureren om privépeering van ExpressRoute te gebruiken:

1. Ga terug naar de virtuele WAN-bronpagina en selecteer de hubbron. Of navigeer van de VPN-site naar de verbonden hub.
1. Selecteer **onder Connectiviteit**VPN **(Site-to-Site)**.
1. Selecteer de ellips (**...**) op de VPN-site via ExpressRoute en selecteer **VPN-verbinding bewerken met deze hub.**
1. Selecteer **Ja**voor **het gebruik van Azure Private IP-adres**. De instelling configureert de hub VPN-gateway om privé-IP-adressen te gebruiken binnen het hubadresbereik op de gateway voor deze verbinding, in plaats van de openbare IP-adressen. Dit zal ervoor zorgen dat het verkeer van het on-premises netwerk de ExpressRoute private peering-paden doorkruist in plaats van het openbare internet te gebruiken voor deze VPN-verbinding. De volgende schermafbeelding toont de instelling.

   ![Instellen voor het gebruik van een privé-IP-adres voor de VPN-verbinding](./media/vpn-over-expressroute/vpn-link-configuration.png)
   
1. Selecteer **Opslaan**.

Nadat u uw wijzigingen hebt opgeslagen, gebruikt de VPN-gateway van de hub de privé-IP-adressen op de VPN-gateway om de IPsec/IKE-verbindingen met het on-premises VPN-apparaat via ExpressRoute tot stand te brengen.

## <a name="4-get-the-private-ip-addresses-for-the-hub-vpn-gateway"></a><a name="associate"></a>4. Haal de privé-IP-adressen voor de hub VPN-gateway

Download de configuratie van het VPN-apparaat om de privé IP-adressen van de hub VPN-gateway te krijgen. U hebt deze adressen nodig om het on-premises VPN-apparaat te configureren.

1. Selecteer op de pagina voor uw hub **VPN (Site-to-Site)** onder **Connectiviteit**.
1. Selecteer boven aan de **pagina Overzicht** de optie **VPN Config downloaden.** 

   Azure maakt een opslagaccount in de brongroep 'microsoft-network-[location],' waar *locatie* de locatie van het WAN is. Nadat u de configuratie hebt toegepast op uw VPN-apparaten, u dit opslagaccount verwijderen.
1. Nadat het bestand is gemaakt, selecteert u de koppeling om het te downloaden.
1. Pas de configuratie toe op uw VPN-apparaat.

### <a name="vpn-device-configuration-file"></a>Configuratiebestand voor VPN-apparaten

Het apparaatconfiguratiebestand bevat de instellingen die u gebruiken wanneer u uw on-premises VPN-apparaat configureert. Wanneer u dit bestand bekijkt, ziet u de volgende informatie:

* **vpnSiteConfiguration**: Deze sectie geeft de apparaatdetails aan die zijn ingesteld als een site die verbinding maakt met het virtuele WAN. Het bevat de naam en het openbare IP-adres van het filiaalapparaat.
* **vpnSiteConnections**: Deze sectie geeft informatie over de volgende instellingen:

    * Adresruimte van het virtuele netwerk van de virtuele hub.<br/>Voorbeeld:
           ```
           "AddressSpace":"10.51.230.0/24"
           ```
    * Adresruimte van de virtuele netwerken die zijn verbonden met de hub.<br>Voorbeeld:
           ```
           "ConnectedSubnets":["10.51.231.0/24"]
            ```
    * IP-adressen van de VPN-gateway van de virtuele hub. Omdat elke verbinding van de VPN-gateway bestaat uit twee tunnels in actieve configuratie, ziet u beide IP-adressen in dit bestand. In dit voorbeeld `Instance0` ziet `Instance1` en voor elke site privé-IP-adressen in plaats van openbare IP-adressen.<br>Voorbeeld:
           ``` 
           "Instance0":"10.51.230.4"
           "Instance1":"10.51.230.5"
           ```
    * Configuratiegegevens voor de VPN-gatewayverbinding, zoals BGP en vooraf gedeelde sleutel. De vooraf gedeelde sleutel wordt automatisch voor u gegenereerd. U de verbinding altijd bewerken op de **pagina Overzicht** voor een aangepaste vooraf gedeelde sleutel.
  
### <a name="example-device-configuration-file"></a>Voorbeeld van een apparaatconfiguratiebestand

```
[{
      "configurationVersion":{
        "LastUpdatedTime":"2019-10-11T05:57:35.1803187Z",
        "Version":"5b096293-edc3-42f1-8f73-68c14a7c4db3"
      },
      "vpnSiteConfiguration":{
        "Name":"VPN-over-ER-site",
        "IPAddress":"172.24.127.211",
        "LinkName":"VPN-over-ER"
      },
      "vpnSiteConnections":[{
        "hubConfiguration":{
          "AddressSpace":"10.51.230.0/24",
          "Region":"West US 2",
          "ConnectedSubnets":["10.51.231.0/24"]
        },
        "gatewayConfiguration":{
          "IpAddresses":{
            "Instance0":"10.51.230.4",
            "Instance1":"10.51.230.5"
          }
        },
        "connectionConfiguration":{
          "IsBgpEnabled":false,
          "PSK":"abc123",
          "IPsecParameters":{"SADataSizeInKilobytes":102400000,"SALifeTimeInSeconds":3600}
        }
      }]
    },
    {
      "configurationVersion":{
        "LastUpdatedTime":"2019-10-11T05:57:35.1803187Z",
        "Version":"fbdb34ea-45f8-425b-9bc2-4751c2c4fee0"
      },
      "vpnSiteConfiguration":{
        "Name":"VPN-over-INet-site",
        "IPAddress":"13.75.195.234",
        "LinkName":"VPN-over-INet"
      },
      "vpnSiteConnections":[{
        "hubConfiguration":{
          "AddressSpace":"10.51.230.0/24",
          "Region":"West US 2",
          "ConnectedSubnets":["10.51.231.0/24"]
        },
        "gatewayConfiguration":{
          "IpAddresses":{
            "Instance0":"51.143.63.104",
            "Instance1":"52.137.90.89"
          }
        },
        "connectionConfiguration":{
          "IsBgpEnabled":false,
          "PSK":"abc123",
          "IPsecParameters":{"SADataSizeInKilobytes":102400000,"SALifeTimeInSeconds":3600}
        }
      }]
}]
```

### <a name="configuring-your-vpn-device"></a>Uw VPN-apparaat configureren

Als u instructies nodig hebt voor het configureren van uw apparaat, kunt u de instructies op de [pagina met configuratiescripts voor VPN-apparaten](~/articles/vpn-gateway/vpn-gateway-about-vpn-devices.md#configscripts) gebruiken. Houd in dat geval wel rekening met de volgende dingen:

* De instructies op de pagina met het VPN-apparaat zijn niet geschreven voor een virtueel WAN. Maar u de virtuele WAN-waarden uit het configuratiebestand gebruiken om uw VPN-apparaat handmatig te configureren. 
* De downloadbare apparaatconfiguratiescripts die voor de VPN-gateway zijn, werken niet voor het virtuele WAN, omdat de configuratie anders is.
* Een nieuwe virtuele WAN kan zowel IKEv1 als IKEv2 ondersteunen.
* Een virtuele WAN kan alleen routegebaseerde VPN-apparaten en apparaatinstructies gebruiken.

## <a name="5-view-your-virtual-wan"></a><a name="viewwan"></a>5. Bekijk uw virtuele WAN

1. Ga naar de virtuele WAN.
1. Op de pagina **Overzicht** vertegenwoordigt elk punt op de kaart een hub.
1. In de sectie **Hubs en verbindingen** u de status van de hub-, site-, regio- en VPN-verbinding bekijken. U ook bytes in en uit bekijken.

## <a name="7-monitor-a-connection"></a><a name="connectmon"></a>7. Een verbinding bewaken

Maak een verbinding om de communicatie tussen een virtuele Azure-machine (VM) en een externe site te bewaken. Zie voor meer informatie over het instellen van een verbindingscontrole de pagina [Netwerkcommunicatie bewaken](~/articles/network-watcher/connection-monitor.md). Het bronveld is het VM-IP in Azure en het doel-IP is het site-IP.

## <a name="8-clean-up-resources"></a><a name="cleanup"></a>8.

Wanneer u deze resources niet meer nodig hebt, u [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) gebruiken om de resourcegroep en alle resources die deze bevat te verwijderen. Voer de volgende powershell-opdracht uit en vervang `myResourceGroup` de naam van uw resourcegroep:

```azurepowershell-interactive
Remove-AzResourceGroup -Name myResourceGroup -Force
```

## <a name="next-steps"></a>Volgende stappen

In dit artikel u een VPN-verbinding maken via privépeering via ExpressRoute met behulp van Virtual WAN. Zie het [Virtual WAN-overzicht voor](virtual-wan-about.md)meer informatie over Virtual WAN en gerelateerde functies.
