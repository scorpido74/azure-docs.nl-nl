---
title: Een site-naar-site-VPN-verbinding maken via ExpressRoute private peering in azure Virtual WAN | Microsoft Docs
description: In deze zelf studie leert u hoe u Azure Virtual WAN kunt gebruiken om een site-naar-site-VPN-verbinding te maken via ExpressRoute-persoonlijke peering.
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: article
ms.date: 10/11/2019
ms.author: cherylmc
Customer intent: I want to connect my on-premises networks to my VNets using S2S VPN connection over my ExpressRoute private peering using Azure Virtual WAN.
ms.openlocfilehash: 6272d6fe6f8c35c06a8121e10be2dd5a2e5512a8
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/04/2019
ms.locfileid: "73515029"
---
# <a name="create-a-site-to-site-vpn-connection-over-expressroute-private-peering-using-azure-virtual-wan"></a>Een site-naar-site-VPN-verbinding maken via ExpressRoute private peering met behulp van Azure Virtual WAN

Dit artikel laat u zien hoe u met behulp van virtuele WAN een IPsec/IKE-VPN-verbinding vanaf uw on-premises netwerk naar Azure kunt maken via de persoonlijke peering van een ExpressRoute-circuit. Dit kan een versleutelde overdracht bieden tussen de on-premises netwerken en virtuele netwerken van Azure via ExpressRoute, zonder het open bare Internet te passeren of open bare IP-adressen te gebruiken.

## <a name="topology-and-routing"></a>Topologie en route ring

In het volgende diagram ziet u een voor beeld van VPN via ExpressRoute particuliere peering-connectiviteit:

![VPN via ExpressRoute](./media/vpn-over-expressroute/vwan-vpn-over-er.png)

Het diagram toont een netwerk binnen het on-premises netwerk dat is verbonden met de Azure hub VPN-gateway via ExpressRoute private peering. De connectiviteits inrichting is eenvoudig:

1. ExpressRoute-verbinding tot stand brengen met een ExpressRoute-circuit en privé-peering
2. De VPN-verbinding tot stand brengen, zoals beschreven in dit document

Een belang rijk aspect van deze configuratie is de route ring tussen de on-premises netwerken en Azure via de ExpressRoute-en VPN-paden.

### <a name="traffic-from-on-premises-networks-to-azure"></a>Verkeer van on-premises netwerken naar Azure

Voor verkeer van on-premises netwerken naar Azure worden de Azure-voor voegsels (met inbegrip van de virtuele hub en alle spoke Virtual Networks die zijn verbonden met de hub) geadverteerd via de ExpressRoute private peering BGP en de VPN BGP. Dit heeft tot gevolg dat twee netwerk routes (paden) naar Azure van de on-premises netwerken. de ene via het met IPsec beveiligde pad en een rechtstreeks over de ExpressRoute **zonder** IPSec-beveiliging. Om ervoor te zorgen dat versleuteling wordt toegepast op de communicatie, moet u ervoor zorgen dat voor het netwerk met VPN-verbinding in het diagram de Azure-routes via on-premises VPN-gateway worden voor rang boven het directe ExpressRoute-pad.

### <a name="traffic-from-azure-to-on-premises-networks"></a>Verkeer van Azure naar on-premises netwerken

Dezelfde vereiste is van toepassing op het verkeer van Azure naar on-premises netwerken. Om ervoor te zorgen dat het IPsec-pad de voor keur heeft boven het directe ExpressRoute-pad (zonder IPsec), hebt u twee opties:

- Adverteer meer specifieke voor voegsels op de VPN BGP-sessie voor het VPN-verbonden netwerk. U kunt een groter bereik aankondigen met het ' VPN-verbonden netwerk ' via ExpressRoute private peering, en vervolgens meer specifieke bereiken in de VPN BGP-sessie. Bijvoorbeeld, Adverteer 10.0.0.0/16 via ExpressRoute en 10.0.1.0/24 via VPN.

- Adverteer onafhankelijke voor voegsels adverteren voor VPN en ExpressRoute. Als de netwerkbereiken met VPN-verbindingen worden ontkoppeld van een ander ExpressRoute verbonden netwerk, kunt u de voor voegsels in respectievelijk de BGP-sessies VPN en ExpressRoute aankondigen. Bijvoorbeeld, Adverteer 10.0.0.0/24 over ExpressRoute en 10.0.1.0/24 via VPN.

In beide voor beelden verzendt Azure verkeer naar 10.0.1.0/24 via de VPN-verbinding in plaats van rechtstreeks via ExpressRoute zonder VPN-beveiliging.

> [!WARNING]
> Als u **dezelfde** voor voegsels aankondigt voor zowel ExpressRoute-als VPN-verbindingen, wordt **het pad ExpressRoute rechtstreeks zonder VPN-beveiliging gebruikt**.
>

## <a name="before-you-begin"></a>Voordat u begint

[!INCLUDE [Before you begin](../../includes/virtual-wan-tutorial-vwan-before-include.md)]

## <a name="openvwan"></a>1. Maak een virtueel WAN en een hub met gateways

De volgende Azure-resources en de bijbehorende on-premises configuraties moeten aanwezig zijn voordat u doorgaat:

1. Een virtueel WAN van Azure
2. Een virtuele WAN-hub met een [ExpressRoute-gateway](virtual-wan-expressroute-portal.md) en een [VPN-gateway](virtual-wan-site-to-site-portal.md)

Raadpleeg [een ExpressRoute-koppeling maken met behulp van Azure Virtual WAN](virtual-wan-expressroute-portal.md) voor de stappen voor het maken van een virtueel WAN van Azure en een hub met een ExpressRoute-koppeling, en [Maak een site-naar-site-verbinding met behulp van Azure Virtual WAN](virtual-wan-site-to-site-portal.md) voor de stappen voor het maken van een VPN gateway in het virtuele WAN.

## <a name="site"></a>2. Maak een site voor het on-premises netwerk

De site bron is hetzelfde als de niet-ExpressRoute VPN-sites voor virtuele WAN. Het belangrijkste voor deel is dat het IP-adres van het on-premises VPN-apparaat nu een privé-IP-adres kan zijn, of dat een openbaar IP-adres in het on-premises netwerk bereikbaar is via ExpressRoute-persoonlijke peering die is gemaakt in stap 1.

> [!NOTE]
> Het IP-adres van het on-premises VPN-apparaat moet deel uitmaken van de adres voorvoegsels die worden geadverteerd naar de virtuele WAN-hub via Azure ExpressRoute private peering.
>

1. Ga naar de Azure Portal in uw browser. Klik op het WAN dat u hebt gemaakt. Klik op de WAN-pagina onder **connectiviteit**op **VPN-sites** om de pagina VPN-sites te openen.

2. Op de pagina **VPN sites** klikt u op **+Site maken**.

3. Vul de volgende velden in op de pagina **Site maken**:

   * **Abonnement** - controleer of het abonnement klopt.
   * **Resource groep** : Selecteer of maak de resource groep die u wilt gebruiken.
   * **Regio** : de Azure-regio voor de bron van de VPN-site.
   * **Naam** : de naam waarmee u naar uw on-premises site wilt verwijzen.
   * **Leverancier van apparaat** : de leverancier van het on-PREMISES VPN-apparaat.
   * **Border Gateway Protocol** : Selecteer ' inschakelen ' als uw on-premises netwerk gebruikmaakt van BGP.
   * **Privié-adresruimte** - dit is de IP-adresruimte op uw on-premises site. Verkeer dat is bestemd voor deze adres ruimte, wordt via de VPN-gateway doorgestuurd naar het on-premises netwerk.
   * **Hubs** : Selecteer een of meer hubs om verbinding te maken met deze VPN-site. Voor de geselecteerde hubs moeten al VPN-gateways zijn gemaakt.

4. Klik op **volgende: koppelingen >** voor de instellingen van de VPN-koppeling:

   * **Naam van koppeling** : de naam waarmee u naar deze verbinding wilt verwijzen.
   * **Provider naam** : de naam van de Internet serviceprovider voor deze site. In het geval van ExpressRoute on-premises netwerk, de naam van de ExpressRoute-service provider.
   * **Snelheid** : de snelheid van de verbinding met de Internet service of het ExpressRoute-circuit.
   * **IP-adres** : het open bare IP-adres van het VPN-apparaat dat zich op uw on-premises site bevindt. Of, in het geval van ExpressRoute on-premises, het privé-IP-adres van het VPN-apparaat via ExpressRoute.

   Als BGP is ingeschakeld, wordt dit toegepast op alle verbindingen die zijn gemaakt voor deze site in Azure. Het configureren van BGP op een virtueel WAN is gelijk aan het configureren van BGP op een Azure VPN-gateway. Uw on-premises BGP-peer adres *mag niet* gelijk zijn aan het IP-adres van uw VPN-apparaat of de VNet-adres ruimte van de VPN-site. Gebruik een ander IP-adres op het VPN-apparaat voor uw BGP-peer-IP. Het kan een adres zijn dat is toegewezen aan de loopback-interface op het apparaat. Het kan echter *geen* APIPA zijn (169,254. *x*. *x*)-adres. Specificeer dit adres in de bijbehorende lokale netwerkgateway die de locatie vertegenwoordigt. Zie [over BGP met Azure VPN gateway](../vpn-gateway/vpn-gateway-bgp-overview.md)voor de BGP-vereisten.

5. Klik op **volgende: controleren + maken >** om de instellings waarden te controleren en de VPN-site te maken. Als u **hubs** hebt geselecteerd om verbinding te maken, wordt de verbinding tot stand gebracht tussen het on-premises netwerk en de hub VPN-gateway.

## <a name="hub"></a>3. werk de instelling voor de VPN-verbinding voor het gebruik van ExpressRoute

Nadat u de VPN-site hebt gemaakt en verbinding maakt met de hub, gebruikt u de volgende stappen om de verbinding te configureren voor het gebruik van ExpressRoute-persoonlijke peering:

1. Ga terug naar de pagina virtuele WAN-bron en klik op de resource van de hub. Of navigeer van de VPN-site naar de verbonden hub.

2. Klik onder **connectiviteit**op **VPN (site-naar-site)**

3. Klik op... op de VPN-site via ExpressRoute en selecteer '**VPN-verbinding met deze hub bewerken**'.

4. Selecteer Ja op**persoonlijk IP-adres van Azure gebruiken**. Met deze instelling configureert u de hub VPN-gateway voor het gebruik van privé-IP-adressen binnen het hub-adres bereik op de gateway voor deze verbinding, in plaats van de open bare IP-adressen. Dit zorgt ervoor dat het verkeer van het on-premises netwerk het ExpressRoute persoonlijke peering-paden overgaat in plaats van het open bare Internet voor deze VPN-verbinding te gebruiken. In de volgende scherm afbeelding ziet u het instellingen venster.

   ![Instelling voor VPN-verbinding](./media/vpn-over-expressroute/vpn-link-configuration.png)
   
5. Klik op **Opslaan**.

Zodra de hub VPN-gateway is opgeslagen, worden de privé-IP-adressen op de VPN-gateway gebruikt om de IPsec/IKE-verbindingen met het on-premises VPN-apparaat via ExpressRoute te maken.

## <a name="associate"></a>4. Haal de hub VPN-gateway privé IP-adressen op

Down load de configuratie van het VPN-apparaat om de privé-IP-adressen van de hub VPN-gateway op te halen. Deze zijn nodig voor het configureren van het on-premises VPN-apparaat.

1. Klik op de pagina voor uw hub op **VPN (site-naar-site)** onder **connectiviteit**

2. Klik boven aan de pagina overzicht op **VPN-configuratie downloaden**. In azure wordt een opslag account gemaakt in de resource groep ' micro soft-Network-[locatie] ', waarbij locatie de locatie van het WAN is. Nadat u de configuratie hebt toegepast op uw VPN-apparaten, kunt u dit storage-account verwijderen.

3. Wanneer het bestand gereed is, klikt u op de koppeling om het te downloaden.

4. Pas de configuratie toe op uw VPN-apparaat.

### <a name="understanding-the-vpn-device-configuration-file"></a>Meer informatie over het configuratiebestand voor uw VPN-apparaat

Het apparaatconfiguratiebestand bevat de instellingen die u dient te gebruiken om uw on-premises VPN-apparaat te configureren. Wanneer u dit bestand bekijkt, ziet u de volgende informatie:

* **vpnSiteConfiguration -** in deze sectie vindt u de apparaatgegevens, ingesteld als een site die verbinding maakt met het virtuele WAN. Hier vindt u ook de naam en het openbare ip-adres van het branch-apparaat.
* **vpnSiteConnections-** Deze sectie bevat informatie over de volgende instellingen:

    * **Adresruimte** van het VNet van de virtuele hub(s)<br/>Voorbeeld:
           ```
           "AddressSpace":"10.51.230.0/24"
           ```
    * **Adresruimte** van de VNets die zijn verbonden met de hub<br>Voorbeeld:
           ```
           "ConnectedSubnets":["10.51.231.0/24"]
            ```
    * **IP-adressen** van de VPN-gateway van de virtuele hub. Omdat elke verbinding van de vpngateway bestaat uit twee tunnels in actief/actief-configuratie, ziet u beide IP-adressen die in dit bestand staan vermeld. In dit voor beeld ziet u ' Instance0 ' en ' Exemplaar1 ' voor elke site, en dit zijn privé-IP-adressen in plaats van open bare IP-adressen.<br>Voorbeeld:
           ``` 
           "Instance0":"10.51.230.4"
           "Instance1":"10.51.230.5"
           ```
    * **Details van de Vpngateway-verbindings configuratie** , zoals BGP, vooraf gedeelde sleutel, enzovoort. De PSK is de vooraf gedeelde sleutel die automatisch voor u wordt gegenereerd. U kunt altijd de verbinding bewerken op de pagina Overzicht om een aangepaste PSK in te stellen.
  
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

* De instructies op de pagina voor VPN-apparaten zijn niet geschreven voor Virtual WAN, maar u kunt de waarden voor Virtual WAN in het configuratiebestand gebruiken om uw VPN-apparaat handmatig te configureren. 
* De downloadbare apparaatconfiguratiescripts voor VPN Gateway werken niet voor Virtual WAN, omdat de configuratie anders is.
* Een nieuwe Virtual WAN kan ondersteuning bieden voor zowel IKEv1 als IKEv2.
* Virtual WAN werkt alleen met op route gebaseerde VPN-apparaten en apparaatinstructies.

## <a name="viewwan"></a>5. uw virtuele WAN weer geven

1. Navigeer naar uw virtuele WAN.

2. Op de pagina Overzicht vertegenwoordigt elk punt op de kaart een hub. Houd de muisaanwijzer boven een punt om de statussamenvatting voor de hub weer te geven.

3. In de sectie Hubs en verbindingen vindt u informatie over de hubstatus, site, regio, VPN-verbindingsstatus en verzonden en ontvangen bytes.

## <a name="viewhealth"></a>6. de status van uw resource weer geven

1. Navigeer naar uw WAN.

2. Klik op de pagina van uw WAN, in de sectie **Ondersteuning en probleemoplossing**, op **Status** en bekijk de status van uw resource.

## <a name="connectmon"></a>7. een verbinding bewaken

Maak een verbinding om de communicatie tussen een Azure-VM en een externe site te bewaken. Zie voor meer informatie over het instellen van een verbindingscontrole de pagina [Netwerkcommunicatie bewaken](~/articles/network-watcher/connection-monitor.md). Het bronveld is het IP-adres van de VM in Azure en het doel-IP-adres is het IP-adres voor de site.

## <a name="cleanup"></a>8. opschonen van resources

Wanneer u deze resources niet meer nodig hebt, kunt u [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) gebruiken om de resource groep en alle resources die deze bevat, te verwijderen. Vervangen 'myResourceGroup' door de naam van uw resourcegroep en voer de volgende PowerShell-opdracht uit:

```azurepowershell-interactive
Remove-AzResourceGroup -Name myResourceGroup -Force
```

## <a name="next-steps"></a>Volgende stappen

Dit artikel helpt u bij het maken van een VPN-verbinding via ExpressRoute private peering met behulp van Virtual WAN. Zie de pagina [overzicht van virtuele WAN](virtual-wan-about.md) voor meer informatie over virtuele WAN-en andere verwante functies.
