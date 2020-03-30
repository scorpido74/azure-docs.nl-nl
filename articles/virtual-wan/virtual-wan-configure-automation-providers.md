---
title: Richtlijnen voor azure Virtual WAN-partners automatisering | Microsoft Documenten
description: In dit artikel kunnen partners Azure Virtual WAN-automatisering instellen.
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: conceptual
ms.date: 02/12/2020
ms.author: cherylmc
ms.openlocfilehash: 7848dda09b39f446dd218b7ce1eb2a07664bcaa6
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "77190422"
---
# <a name="automation-guidelines-for-virtual-wan-partners"></a>Automatiseringsrichtlijnen voor Virtual WAN-partners

In dit artikel u begrijpen hoe u de automatiseringsomgeving instellen om een branch-apparaat (een on-premises VPN-apparaat of SDWAN CPE) voor Azure Virtual WAN te verbinden en te configureren. Als u een provider bent die brancheapparaten biedt die geschikt zijn voor VPN-connectiviteit via IPsec/IKEv2 of IPsec/IKEv1, is dit artikel iets voor u.

Een filiaalapparaat (een on-premises VPN-apparaat of SDWAN CPE) gebruikt doorgaans een controller/apparaatdashboard om te worden ingericht. Beheerders van sd-WAN-oplossingen kunnen vaak een beheerconsole gebruiken om een apparaat vooraf in te richten voordat het wordt aangesloten op het netwerk. Dit VPN-apparaat krijgt zijn controlevlaklogica van een controller. De VPN-apparaat of SD-WAN-controller kan Azure API's gebruiken om de connectiviteit met Azure Virtual WAN te automatiseren. Voor dit type verbinding moet het on-premises apparaat een extern gericht openbaar IP-adres hebben dat eraan is toegewezen.

## <a name="before-you-begin-automating"></a><a name ="before"></a>Voordat u begint met automatiseren

* Controleer of uw apparaat IPsec IKEv1/IKEv2 ondersteunt. Zie [standaardbeleid](#default).
* Bekijk de [REST API's](#additional) die u gebruikt om de connectiviteit met Azure Virtual WAN te automatiseren.
* Test de portalervaring van Azure Virtual WAN.
* Bepaal vervolgens welk deel van de connectiviteitsstappen u wilt automatiseren. We raden aan om het automatiseren van:

  * Toegangsbeheer
  * Uploaden van informatie over vertakkingsapparaten naar Azure Virtual WAN
  * Azure-configuratie downloaden en connectiviteit instellen vanaf branch-apparaat in Azure Virtual WAN

### <a name="additional-information"></a><a name ="additional"></a>Aanvullende informatie

* [REST API](https://docs.microsoft.com/rest/api/virtualwan/virtualhubs) om het maken van Virtual Hub te automatiseren
* [REST API](https://docs.microsoft.com/rest/api/virtualwan/vpngateways) voor automatiseren Azure VPN-gateway voor Virtual WAN
* [REST API](https://docs.microsoft.com/rest/api/virtualwan/vpnconnections) om een VPNSite te verbinden met een Azure VPN Hub
* [Standaard-IPsec-beleid](#default)

## <a name="customer-experience"></a><a name ="ae"></a>Klantervaring

Inzicht in de verwachte klantervaring in combinatie met Azure Virtual WAN.

  1. Een virtuele WAN-gebruiker start het proces meestal door een virtuele WAN-bron te maken.
  2. De gebruiker stelt een serviceprincipal-gebaseerde brongroep toegang voor het on-premises systeem (uw branch controller of VPN device provisioning software) om branch info te schrijven in Azure Virtual WAN.
  3. De gebruiker kan op dit moment besluiten om in te loggen op uw gebruikersinterface en de servicehoofdreferenties in te stellen. Zodra dat is voltooid, moet uw controller in staat zijn om branch informatie te uploaden met de automatisering die u zal bieden. Het handmatige equivalent hiervan aan de Azure-kant is 'Site maken'.
  4. Zodra de sitegegevens (branch device) beschikbaar zijn in Azure, verbindt de gebruiker de site met een hub. Een virtuele hub is een door Microsoft beheerd virtueel netwerk. De hub bevat verschillende service-eindpunten die verbindingen vanaf uw on-premises netwerk (vpnsite) mogelijk maken. De hub is de kern van uw netwerk in een regio. Er kan slechts één hub per Azure-regio zijn en het vpn-eindpunt (vpngateway) erin wordt tijdens dit proces gemaakt. De VPN-gateway is een schaalbare gateway die op de juiste manier wordt gegroot op basis van bandbreedte en verbindingsbehoeften. U ervoor kiezen om virtuele hub en vpngateway-creatie te automatiseren vanaf het dashboard van uw branch-apparaatcontroller.
  5. Zodra de virtuele hub is gekoppeld aan de site, wordt een configuratiebestand gegenereerd voor de gebruiker om handmatig te downloaden. Dit is waar uw automatisering komt in en maakt de gebruikerservaring naadloos. In plaats van dat de gebruiker het filiaalapparaat handmatig moet downloaden en configureren, u de automatisering instellen en minimale klikervaring op uw gebruikersinterface bieden, waardoor typische verbindingsproblemen zoals een mismatch met gedeelde sleutels, IPSec-parameter mismatch, configuratie bestand leesbaarheid etc.
  6. Aan het einde van deze stap in uw oplossing heeft de gebruiker een naadloze site-to-site verbinding tussen het filiaalapparaat en de virtuele hub. U ook extra verbindingen instellen voor andere hubs. Elke verbinding is een actief-actieve tunnel. Uw klant kan ervoor kiezen om een andere ISP te gebruiken voor elk van de links voor de tunnel.
  7. Overweeg om probleemoplossings- en bewakingsmogelijkheden te bieden in de CPE-beheerinterface. Typische scenario's zijn "Klant die geen toegang heeft tot Azure-bronnen vanwege een CPE-probleem", "IPsec-parameters weergeven aan de CPE-kant" enz.

## <a name="automation-details"></a><a name ="understand"></a>Automatiseringsgegevens

###  <a name="access-control"></a><a name="access"></a>Toegangsbeheer

Klanten moeten in staat zijn om de juiste toegangscontrole voor Virtual WAN in de gebruikersinterface van het apparaat in te stellen. Dit wordt aanbevolen met behulp van een Azure Service Principal. Service principal-gebaseerde toegang biedt de apparaatcontroller de juiste verificatie om branch-informatie te uploaden. Zie [Serviceprincipal maken](../active-directory/develop/howto-create-service-principal-portal.md#create-an-azure-active-directory-application)voor meer informatie . Hoewel deze functionaliteit buiten het Azure Virtual WAN-aanbod valt, vermelden we hieronder de typische stappen die zijn genomen om toegang in Azure in te stellen, waarna de relevante gegevens worden ingevoerd in het dashboard voor apparaatbeheer

* Maak een Azure Active Directory-toepassing voor uw on-premises apparaatcontroller.
* Toepassings-id en verificatiesleutel ophalen
* Tenant-id ophalen
* Toepassing toewijzen aan rol 'Inzender'

###  <a name="upload-branch-device-information"></a><a name="branch"></a>Informatie over branch-apparaten uploaden

U moet de gebruikerservaring ontwerpen om branch-informatie (on-premises site) naar Azure te uploaden. U [REST API's](https://docs.microsoft.com/rest/api/virtualwan/vpnsites) voor VPNSite gebruiken om de site-informatie in Virtual WAN te maken. U alle Branch SDWAN/VPN-apparaten leveren of waar nodig apparaataanpassingen selecteren.

### <a name="device-configuration-download-and-connectivity"></a><a name="device"></a>Downloaden en connectiviteit van apparaatconfiguratie

Deze stap omvat het downloaden van Azure-configuratie en het instellen van connectiviteit vanaf het branch-apparaat in Azure Virtual WAN. In deze stap zou een klant die geen provider gebruikt, de Azure-configuratie handmatig downloaden en toepassen op hun on-premises SDWAN/VPN-apparaat. Als provider moet u deze stap automatiseren. Bekijk de download [REST API's](https://docs.microsoft.com/rest/api/virtualwan/vpnsitesconfiguration/download) voor meer informatie. De apparaatcontroller kan de REST API van 'GetVpnConfiguration' aanroepen om de Azure-configuratie te downloaden.

**Configuratienotities**

  * Als Azure VNets zijn gekoppeld aan de virtuele hub, worden ze weergegeven als ConnectedSubnets.
  * VPN-connectiviteit maakt gebruik van routegebaseerde configuratie en ondersteunt zowel IKEv1- als IKEv2-protocollen.

## <a name="device-configuration-file"></a><a name="devicefile"></a>Apparaatconfiguratiebestand

Het apparaatconfiguratiebestand bevat de instellingen die u dient te gebruiken om uw on-premises VPN-apparaat te configureren. Wanneer u dit bestand bekijkt, ziet u de volgende informatie:

* **vpnSiteConfiguration -** in deze sectie vindt u de apparaatgegevens, ingesteld als een site die verbinding maakt met het virtuele WAN. Hier vindt u ook de naam en het openbare ip-adres van het branch-apparaat.
* **vpnSiteConnections -** deze sectie bevat informatie over het volgende:

    * **Adresruimte** van de virtuele hub(s) VNet.<br>Voorbeeld:
 
        ```
        "AddressSpace":"10.1.0.0/24"
        ```
    * **Adresruimte** van de VNets die zijn verbonden met de hub.<br>Voorbeeld:

         ```
        "ConnectedSubnets":["10.2.0.0/16","10.3.0.0/16"]
         ```
    * **IP-adressen** van de VPN-gateway van de virtuele hub. Omdat voor de VPN-gateway elke verbinding is opgebouwd uit 2 tunnels in een actief-/actief-configuratie, worden beide IP-adressen in dit bestand vermeld. In dit voorbeeld ziet u voor elke site 'Instance0' en 'Instance1'.<br>Voorbeeld:

        ``` 
        "Instance0":"104.45.18.186"
        "Instance1":"104.45.13.195"
        ```
    * **Vpngateway-configuratiedetails** zoals BGP, vooraf gedeelde sleutel enz. De PSK is de vooraf gedeelde sleutel die automatisch voor u wordt gegenereerd. U kunt altijd de verbinding bewerken op de pagina Overzicht om een aangepaste PSK in te stellen.
  
**Voorbeeld van een apparaatconfiguratiebestand**

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
                  "10.3.0.0/16"
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

## <a name="connectivity-details"></a><a name="default"></a>Details van de connectiviteit

Uw on-premises SDWAN/VPN-apparaat of SD-WAN-configuratie moet overeenkomen met of de volgende algoritmen en parameters bevatten, die u opgeeft in het Azure IPsec/IKE-beleid.

* IKE-versleutelingsalgoritme
* IKE-integriteitsalgoritme
* DH-groep
* IPsec-versleutelingsalgoritme
* IPsec-integriteitsalgoritme
* PFS-groep

### <a name="default-policies-for-ipsec-connectivity"></a><a name="default"></a>Standaardbeleid voor IPsec-connectiviteit

[!INCLUDE [IPsec Default](../../includes/virtual-wan-ipsec-include.md)]

### <a name="custom-policies-for-ipsec-connectivity"></a><a name="custom"></a>Aangepast beleid voor IPsec-connectiviteit

[!INCLUDE [IPsec Custom](../../includes/virtual-wan-ipsec-custom-include.md)]

## <a name="next-steps"></a>Volgende stappen

Zie Over Azure Virtual [WAN](virtual-wan-about.md) en de [veelgestelde vragen](virtual-wan-faq.md)over Azure Virtual WAN en de veelgestelde vragen over Azure Virtual WAN .

Voor meer informatie u <azurevirtualwan@microsoft.com>een e-mail sturen naar. Vermeld uw bedrijfsnaam tussen '[ ]' in de onderwerpregel.
