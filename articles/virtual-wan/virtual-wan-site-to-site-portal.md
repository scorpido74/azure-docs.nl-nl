---
title: 'Virtueel WAN van Azure: Maak site-naar-site-verbindingen'
description: In deze zelfstudie leert u hoe u Azure Virtual WAN kunt gebruiken om een site-naar-site-VPN-verbinding te maken met Azure.
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: tutorial
ms.date: 11/04/2019
ms.author: cherylmc
Customer intent: As someone with a networking background, I want to connect my local site to my VNets using Virtual WAN and I don't want to go through a Virtual WAN partner.
ms.openlocfilehash: 10b9dba2f54a2d20b0cb405285772f8c3d74b3a4
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/25/2019
ms.locfileid: "75450839"
---
# <a name="tutorial-create-a-site-to-site-connection-using-azure-virtual-wan"></a>Zelfstudie: Een site-naar-site-verbinding maken met Azure Virtual WAN

In deze zelfstudie leert u hoe u Virtual WAN kunt gebruiken om verbinding te maken met uw resources in Azure via een VPN-verbinding met IPsec/IKE (IKEv1 en IKEv2). Voor dit type verbinding moet er on-premises een VPN-apparaat aanwezig zijn waaraan een extern openbaar IP-adres is toegewezen. Zie voor meer informatie over Virtual WAN het [Overzicht van Virtual WAN](virtual-wan-about.md).

In deze zelfstudie leert u het volgende:

> [!div class="checklist"]
> * Een virtueel WAN maken
> * Een hub maken
> * Een site maken
> * Een site verbinden met een hub
> * Een VPN-site verbinden met een hub
> * Een VNet verbinden met een hub
> * Een configuratie bestand downloaden
> * Uw virtuele WAN weergeven

> [!NOTE]
> Als u veel sites hebt, zou u normaal gesproken een [Virtual WAN-partner](https://aka.ms/virtualwan) gebruiken om deze configuratie te maken. U kunt deze configuratie echter ook zelf maken als bekend bent met netwerktechnologie en uw eigen VPN-apparaat kunt configureren.
>

![Virtual WAN-diagram](./media/virtual-wan-about/virtualwan.png)

## <a name="before-you-begin"></a>Voordat u begint

Controleer voordat u met de configuratie begint of u aan de volgende criteria hebt voldaan:

* U hebt een virtueel netwerk waarmee u verbinding wilt maken. Controleer of geen van de subnetten van uw on-premises netwerken overlapt met de virtuele netwerken waarmee u verbinding wilt maken. Als u een virtueel netwerk in de Azure Portal wilt maken, raadpleegt u [Quick](../virtual-network/quick-create-portal.md)start.

* Het virtuele netwerk heeft geen virtuele netwerk gateways. Als uw virtuele netwerk een gateway heeft (VPN of ExpressRoute), moet u alle gateways verwijderen. Voor deze configuratie moeten virtuele netwerken in plaats daarvan zijn verbonden met de virtuele WAN hub-gateway.

* Zorg dat u een IP-adresbereik krijgt voor uw hubregio. De hub is een virtueel netwerk dat wordt gemaakt en gebruikt door Virtual WAN. Het adres bereik dat u voor de hub opgeeft, mag niet overlappen met een van de bestaande virtuele netwerken waarmee u verbinding maakt. Dit bereik mag ook niet overlappen met de adresbereiken waarmee u on-premises verbinding wilt maken. Als u niet bekend bent met de IP-adresbereiken die zich in uw on-premises netwerk configuratie bevinden, coördineert u met iemand die deze gegevens voor u kan opgeven.

* Als u nog geen abonnement op Azure hebt, maakt u een [gratis account](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) aan.

## <a name="openvwan"></a>Een virtueel WAN maken

Ga in een browser naar het Azure Portal en meld u aan met uw Azure-account.

1. Ga naar de virtuele WAN-pagina. Klik in de portal op **+Een resource maken**. Typ **Virtual WAN** in het zoekvak en selecteer ENTER.
2. Selecteer **virtueel WAN** in de resultaten. Klik op de pagina virtueel WAN op **maken** om de pagina WAN maken te openen.
3. Vul op de pagina **WAN maken** op het tabblad **basis beginselen** de volgende velden in:

   ![Virtual WAN](./media/virtual-wan-site-to-site-portal/vwan.png)

   * **Abonnement** - selecteer het abonnement dat u wilt gebruiken.
   * **Resource groep** : nieuwe maken of bestaande gebruiken.
   * **Locatie van resource groep** : Kies een resource locatie in de vervolg keuzelijst. Een WAN een globale resource en bevindt zich niet in een bepaalde regio. U moet echter een regio selecteren om de WAN-resource die u maakt eenvoudiger te kunnen beheren en vinden.
   * **Naam** : Typ de naam die u voor uw WAN wilt aanroepen.
   * **Type:** Basic of Standard. Als u een WAN Basic maakt, kunt u alleen een Basic-hub maken. Basis hubs zijn alleen geschikt voor VPN-verbindingen tussen sites.
4. Wanneer u klaar bent met het invullen van de velden, selecteert u **controleren + maken**.
5. Wanneer de validatie is geslaagd, selecteert u **maken** om het virtuele WAN te maken.

## <a name="hub"></a>Een hub maken

Een hub is een virtueel netwerk dat gateways kan bevatten voor site-naar-site-, ExpressRoute-of punt-naar-site-functionaliteit. Zodra de hub is gemaakt, worden u kosten in rekening gebracht voor de hub, zelfs als u geen sites hebt toegevoegd. Het duurt 30 minuten om de site-naar-site-VPN-gateway in de virtuele hub te maken.

[!INCLUDE [Create a hub](../../includes/virtual-wan-tutorial-s2s-hub-include.md)]

## <a name="site"></a>Een site maken

U bent nu klaar om de sites te maken die overeenkomen met uw fysieke locaties. Maak het aantal sites dat u nodig hebt voor uw fysieke locaties. Als u bijvoorbeeld een filiaal in NY, een filiaal in Londen en een filiaal in LA hebt, maakt u drie afzonderlijke sites. Deze sites bevatten de eindpunten voor uw on-premises VPN-apparaat. U kunt Maxi maal 1000 sites per virtuele hub maken in een virtueel WAN. Als u meerdere hubs had, kunt u 1000 maken per hub. Als u een virtuele WAN-partner (link insert) CPE-apparaat hebt, raadpleegt u deze voor meer informatie over automatisering naar Azure. Doorgaans impliceert Automation een eenvoudige klik ervaring voor het exporteren van grootschalige vertakkings gegevens naar Azure en het instellen van connectiviteit vanaf de CPE tot Azure Virtual WAN VPN gateway. Zie [Automation-richt lijnen van Azure naar CPE-partners](virtual-wan-configure-automation-providers.md)voor meer informatie.

[!INCLUDE [Create a site](../../includes/virtual-wan-tutorial-s2s-site-include.md)]

## <a name="connectsites"></a>De VPN-site verbinden met de hub

In deze stap verbindt u uw VPN-site met de hub.

[!INCLUDE [Connect VPN sites](../../includes/virtual-wan-tutorial-s2s-connect-vpn-site-include.md)]

## <a name="vnet"></a>De VNet verbinden met de hub

In deze stap maakt u de verbinding tussen uw hub en een VNet. Herhaal deze stappen voor elk VNet dat u wilt verbinden.

1. Klik op de pagina voor uw virtuele WAN op **Virtuele netwerkverbinding**.
2. Klik op de pagina Virtuele netwerkverbinding op **+Verbinding toevoegen**.
3. Vul de volgende velden in op de pagina **Verbinding toevoegen**:

    * **Verbindingsnaam** - voer een naam in voor uw verbinding.
    * **Hubs** - selecteer de hub die u wilt koppelen aan deze verbinding.
    * **Abonnement** - controleer of het abonnement klopt.
    * **Virtueel netwerk** - selecteer het virtuele netwerk dat met deze hub wilt verbinden. Het virtuele netwerk mag geen bestaande virtuele netwerkgateway hebben.
4. Klik op **OK** om de virtuele netwerk verbinding te maken.

## <a name="device"></a>VPN-configuratie downloaden

Gebruik de VPN-apparaatconfiguratie om uw on-premises VPN-apparaat te configureren.

1. Klik op de pagina voor uw virtuele WAN op **Overzicht**.
2. Klik boven aan de VPNSite-pagina van de **hub->** op **VPN-configuratie downloaden**. In azure wordt een opslag account gemaakt in de resource groep ' micro soft-Network-[locatie] ', waarbij locatie de locatie van het WAN is. Nadat u de configuratie hebt toegepast op uw VPN-apparaten, kunt u dit storage-account verwijderen.
3. Wanneer het bestand gereed is, klikt u op de koppeling om het te downloaden.
4. Pas de configuratie toe op uw on-premises VPN-apparaat.

### <a name="understanding-the-vpn-device-configuration-file"></a>Meer informatie over het configuratiebestand voor uw VPN-apparaat

Het apparaatconfiguratiebestand bevat de instellingen die u dient te gebruiken om uw on-premises VPN-apparaat te configureren. Wanneer u dit bestand bekijkt, ziet u de volgende informatie:

* **vpnSiteConfiguration -** in deze sectie vindt u de apparaatgegevens, ingesteld als een site die verbinding maakt met het virtuele WAN. Hier vindt u ook de naam en het openbare ip-adres van het branch-apparaat.
* **vpnSiteConnections-** Deze sectie bevat informatie over de volgende instellingen:

    * **Adresruimte** van het VNet van de virtuele hub(s)<br>Voorbeeld:
 
        ```
        "AddressSpace":"10.1.0.0/24"
        ```
    * **Adresruimte** van de VNets die zijn verbonden met de hub<br>Voorbeeld:

         ```
        "ConnectedSubnets":["10.2.0.0/16","10.30.0.0/16"]
         ```
    * **IP-adressen** van de VPN-gateway van de virtuele hub. Omdat elke verbinding van de vpngateway bestaat uit twee tunnels in actief/actief-configuratie, ziet u beide IP-adressen die in dit bestand staan vermeld. In dit voorbeeld ziet u voor elke site 'Instance0' en 'Instance1'.<br>Voorbeeld:

        ``` 
        "Instance0":"104.45.18.186"
        "Instance1":"104.45.13.195"
        ```
    * **Details van de Vpngateway-verbindings configuratie** , zoals BGP, vooraf gedeelde sleutel, enzovoort. De PSK is de vooraf gedeelde sleutel die automatisch voor u wordt gegenereerd. U kunt altijd de verbinding bewerken op de pagina Overzicht om een aangepaste PSK in te stellen.
  
### <a name="example-device-configuration-file"></a>Voorbeeld van een apparaatconfiguratiebestand

  ```
  { 
      "configurationVersion":{ 
         "LastUpdatedTime":"2018-07-03T18:29:49.8405161Z",
         "Version":"r403583d-9c82-4cb8-8570-1cbbcd9983b5"
      },
      "vpnSiteConfiguration":{ 
         "Name":"testsite1",
         "IPAddress":"73.239.3.208"
      },
      "vpnSiteConnections":[ 
         { 
            "hubConfiguration":{ 
               "AddressSpace":"10.1.0.0/24",
               "Region":"West Europe",
               "ConnectedSubnets":[ 
                  "10.2.0.0/16",
                  "10.30.0.0/16"
               ]
            },
            "gatewayConfiguration":{ 
               "IpAddresses":{ 
                  "Instance0":"104.45.18.186",
                  "Instance1":"104.45.13.195"
               }
            },
            "connectionConfiguration":{ 
               "IsBgpEnabled":false,
               "PSK":"bkOWe5dPPqkx0DfFE3tyuP7y3oYqAEbI",
               "IPsecParameters":{ 
                  "SADataSizeInKilobytes":102400000,
                  "SALifeTimeInSeconds":3600
               }
            }
         }
      ]
   },
   { 
      "configurationVersion":{ 
         "LastUpdatedTime":"2018-07-03T18:29:49.8405161Z",
         "Version":"1f33f891-e1ab-42b8-8d8c-c024d337bcac"
      },
      "vpnSiteConfiguration":{ 
         "Name":" testsite2",
         "IPAddress":"66.193.205.122"
      },
      "vpnSiteConnections":[ 
         { 
            "hubConfiguration":{ 
               "AddressSpace":"10.1.0.0/24",
               "Region":"West Europe"
            },
            "gatewayConfiguration":{ 
               "IpAddresses":{ 
                  "Instance0":"104.45.18.187",
                  "Instance1":"104.45.13.195"
               }
            },
            "connectionConfiguration":{ 
               "IsBgpEnabled":false,
               "PSK":"XzODPyAYQqFs4ai9WzrJour0qLzeg7Qg",
               "IPsecParameters":{ 
                  "SADataSizeInKilobytes":102400000,
                  "SALifeTimeInSeconds":3600
               }
            }
         }
      ]
   },
   { 
      "configurationVersion":{ 
         "LastUpdatedTime":"2018-07-03T18:29:49.8405161Z",
         "Version":"cd1e4a23-96bd-43a9-93b5-b51c2a945c7"
      },
      "vpnSiteConfiguration":{ 
         "Name":" testsite3",
         "IPAddress":"182.71.123.228"
      },
      "vpnSiteConnections":[ 
         { 
            "hubConfiguration":{ 
               "AddressSpace":"10.1.0.0/24",
               "Region":"West Europe"
            },
            "gatewayConfiguration":{ 
               "IpAddresses":{ 
                  "Instance0":"104.45.18.187",
                  "Instance1":"104.45.13.195"
               }
            },
            "connectionConfiguration":{ 
               "IsBgpEnabled":false,
               "PSK":"YLkSdSYd4wjjEThR3aIxaXaqNdxUwSo9",
               "IPsecParameters":{ 
                  "SADataSizeInKilobytes":102400000,
                  "SALifeTimeInSeconds":3600
               }
            }
         }
      ]
   }
  ```

### <a name="configuring-your-vpn-device"></a>Uw VPN-apparaat configureren

>[!NOTE]
> Als u met een Azure Virtual WAN-oplossing van een partner werkt, wordt het VPN-apparaat automatisch geconfigureerd. De apparaatcontroller haalt het configuratiebestand op uit Azure en past dit toe op het apparaat om zo de verbinding met Azure in te stellen. U hoeft in dat geval dus niet te weten hoe u uw VPN-apparaat handmatig configureert.
>

Als u instructies nodig hebt voor het configureren van uw apparaat, kunt u de instructies op de [pagina met configuratiescripts voor VPN-apparaten](~/articles/vpn-gateway/vpn-gateway-about-vpn-devices.md#configscripts) gebruiken. Houd in dat geval wel rekening met de volgende dingen:

* De instructies op de pagina voor VPN-apparaten zijn niet geschreven voor Virtual WAN, maar u kunt de waarden voor Virtual WAN in het configuratiebestand gebruiken om uw VPN-apparaat handmatig te configureren. 
* De downloadbare apparaatconfiguratiescripts voor VPN Gateway werken niet voor Virtual WAN, omdat de configuratie anders is.
* Een nieuw virtueel WAN kan zowel IKEv1 als IKEv2 ondersteunen.
* Virtuele WAN kan gebruikmaken van op beleid gebaseerde VPN-apparaten en-instructies op basis van route ring.

## <a name="viewwan"></a>Uw virtuele WAN weer geven

1. Navigeer naar uw virtuele WAN.
2. Op de **overzichts** pagina vertegenwoordigt elk punt op de kaart een hub. Beweeg de muis aanwijzer over een wille keurig punt om het status overzicht van de hub, de verbindings status en de binnenkomende en uitgaande bytes weer te geven.
3. In de sectie hubs en verbindingen kunt u de status van de hub, VPN-sites, enzovoort weer geven. U kunt klikken op een specifieke spil naam en naar de VPN-site navigeren voor meer informatie.

## <a name="next-steps"></a>Volgende stappen

Zie voor meer informatie over Virtual WAN de pagina [Overzicht van Virtual WAN](virtual-wan-about.md).
