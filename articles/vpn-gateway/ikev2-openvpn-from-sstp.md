---
title: Overgang naar OpenVPN of IKEv2 van SSTP | Azure VPN-gateway
description: In dit artikel u inzicht krijgen in de manieren om de 128 gelijktijdige verbindingslimiet van SSTP te overschrijden.
services: vpn-gateway
author: anzaman
ms.service: vpn-gateway
ms.topic: conceptual
ms.date: 03/22/2020
ms.author: alzam
ms.openlocfilehash: 60c3a7f4f12993f475b3d03fd0915971d7673a5c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80143103"
---
# <a name="transition-to-openvpn-protocol-or-ikev2-from-sstp"></a>Overgang naar OpenVPN-protocol of IKEv2 van SSTP

Met een point-to-site-VPN-gatewayverbinding (P2S) kunt u vanaf een afzonderlijke clientcomputer een beveiligde verbinding maken met uw virtuele netwerk. Een P2S-verbinding wordt tot stand gebracht door deze te starten vanaf de clientcomputer. Dit artikel is van toepassing op het Resource Manager-implementatiemodel en gaat over manieren om de 128 gelijktijdige verbindingslimiet van SSTP te overwinnen door over te stappen op OpenVPN-protocol of IKEv2.

## <a name="what-protocol-does-p2s-use"></a><a name="protocol"></a>Welk protocol gebruikt P2S?

Point-to-site VPN kan een van de volgende protocollen gebruiken:

* **OpenVPN&reg; Protocol**, een SSL/TLS gebaseerd VPN protocol. Een SSL VPN-oplossing kan firewalls binnendringen, omdat de meeste firewalls TCP-poort 443 uitgaand openen, die SSL gebruikt. OpenVPN kan worden gebruikt om verbinding te maken vanaf Android, iOS (versies 11.0 en hoger), Windows, Linux en Mac-apparaten (OSX-versies 10.13 en hoger).

* **Secure Socket Tunneling Protocol (SSTP),** een eigen SSL-gebaseerd VPN-protocol. Een SSL VPN-oplossing kan firewalls binnendringen, omdat de meeste firewalls TCP-poort 443 uitgaand openen, die SSL gebruikt. SSTP wordt alleen ondersteund op Windows-apparaten. Azure ondersteunt alle versies van Windows met SSTP (Windows 7 en hoger). **SSTP ondersteunt maximaal 128 gelijktijdige verbindingen, ongeacht de gateway SKU.**

* IKEv2 VPN, een op standaarden gebaseerde IPsec VPN-oplossing. IKEv2 VPN kan worden gebruikt om verbinding te maken vanaf Mac-apparaten (OSX-versie 10.11 en hoger).


>[!NOTE]
>IKEv2 en OpenVPN voor P2S zijn alleen beschikbaar voor het Resource Manager-implementatiemodel. Ze zijn niet beschikbaar voor het klassieke implementatiemodel. Basic gateway SKU ondersteunt geen IKEv2- of OpenVPN-protocollen. Als u de basisSKU gebruikt, moet u een sku-virtuele netwerkgateway voor productie verwijderen en opnieuw maken.
>

## <a name="migrating-from-sspt-to-ikev2-or-openvpn"></a>Migreren van SSPT naar IKEv2 of OpenVPN

Er kunnen gevallen zijn waarin u meer dan 128 gelijktijdige P2S-verbinding met een VPN-gateway wilt ondersteunen, maar SSTP gebruikt. In een dergelijk geval moet u overstappen naar IKEv2- of OpenVPN-protocol.

### <a name="option-1---add-ikev2-in-addition-to-sstp-on-the-gateway"></a>Optie 1 - Voeg IKEv2 toe naast SSTP op de Gateway

Dit is de eenvoudigste optie. SSTP en IKEv2 kunnen naast elkaar bestaan op dezelfde gateway en geven u een hoger aantal gelijktijdige verbindingen. U IKEv2 eenvoudig inschakelen op de bestaande gateway en de client opnieuw downloaden.

Het toevoegen van IKEv2 aan een bestaande SSTP VPN-gateway heeft geen invloed op bestaande clients en u ze configureren om IKEv2 in kleine batches te gebruiken of gewoon de nieuwe clients configureren om IKEv2 te gebruiken. Als een Windows-client is geconfigureerd voor zowel SSTP en IKEv2, zal het proberen om verbinding te maken met IKEV2 eerste en als dat niet lukt, zal het terug vallen naar SSTP.

**IKEv2 maakt gebruik van niet-standaard UDP-poorten, zodat u ervoor moet zorgen dat deze poorten niet worden geblokkeerd op de firewall van de gebruiker. De poorten in gebruik zijn UDP 500 en 4500.**

Als u IKEv2 wilt toevoegen aan een bestaande gateway, gaat u gewoon naar het tabblad 'point-to-site-configuratie' onder de Virtuele netwerkgateway in portal en selecteert u **IKEv2 en SSTP (SSL)** in de vervolgkeuzelijst.

![point-to-site](./media/ikev2-openvpn-from-sstp/sstptoikev2.png "IKEv2")


### <a name="option-2---remove-sstp-and-enable-openvpn-on-the-gateway"></a>Optie 2 - Verwijder SSTP en schakel OpenVPN in op de Gateway

Aangezien SSTP en OpenVPN beide TLS-gebaseerd e-protocol zijn, kunnen ze niet naast elkaar bestaan op dezelfde gateway. Als u besluit om van SSTP naar OpenVPN te gaan, moet u SSTP uitschakelen en OpenVPN op de gateway inschakelen. Deze bewerking zorgt ervoor dat de bestaande clients de verbinding met de VPN-gateway verliezen totdat het nieuwe profiel is geconfigureerd op de client.

U OpenVPN samen met IKEv2 inschakelen als u dat wenst. OpenVPN is TLS-gebaseerd en maakt gebruik van de standaard TCP 443-poort. Als u wilt overschakelen naar OpenVPN, gaat u naar het tabblad 'point-to-site-configuratie' onder de Virtuele netwerkgateway in portal en selecteert u **OpenVPN (SSL)** of **IKEv2 en OpenVPN (SSL)** in de vervolgkeuzelijst.

![point-to-site](./media/ikev2-openvpn-from-sstp/sstptoopenvpn.png "OpenVPN")

Zodra de gateway is geconfigureerd, kunnen bestaande clients pas verbinding maken nadat u [de OpenVPN-clients implementeert en configureert.](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-howto-openvpn-clients)

Als u Windows 10 gebruikt, u ook de [Azure VPN-client voor Windows](https://docs.microsoft.com/azure/vpn-gateway/openvpn-azure-ad-client#to-download-the-azure-vpn-client) gebruiken


## <a name="frequently-asked-questions"></a>Veelgestelde vragen
### <a name="what-are-the-client-configuration-requirements"></a>Wat zijn de vereisten voor clientconfiguratie?

>[!NOTE]
>Voor Windows-clients moet u beheerdersrechten op het clientapparaat hebben om de VPN-verbinding van het clientapparaat naar Azure te starten.
>

Gebruikers gebruiken de native VPN-clients op Windows- en Mac-apparaten voor P2S. Azure biedt een zip-bestand voor VPN-clientconfiguratie dat instellingen bevat die vereist zijn door deze native clients om verbinding te maken met Azure.

* Voor Windows-apparaten bestaat de VPN-clientconfiguratie uit een installatiepakket dat gebruikers op hun apparaten installeren.
* Voor Mac-apparaten bestaat het uit het mobileconfig-bestand dat gebruikers op hun apparaten installeren.

Het zip-bestand bevat ook de waarden van enkele belangrijke instellingen aan de Azure-kant die u gebruiken om uw eigen profiel voor deze apparaten te maken. Enkele van de waarden zijn het VPN-gatewayadres, geconfigureerde tunneltypen, routes en het rootcertificaat voor gatewayvalidatie.

>[!NOTE]
>[!INCLUDE [TLS version changes](../../includes/vpn-gateway-tls-change.md)]
>

### <a name="which-gateway-skus-support-p2s-vpn"></a><a name="gwsku"></a>Welke gateway SKU's ondersteunen P2S VPN?

[!INCLUDE [aggregate throughput sku](../../includes/vpn-gateway-table-gwtype-aggtput-include.md)]

* Zie [Instellingen voor VPN-gateway voor](vpn-gateway-about-vpn-gateway-settings.md#gwsku)gateway-SKU-aanbevelingen.

>[!NOTE]
>De basis-SKU biedt geen ondersteuning voor IKEv2- of RADIUS-verificatie.
>

### <a name="what-ikeipsec-policies-are-configured-on-vpn-gateways-for-p2s"></a><a name="IKE/IPsec policies"></a>Welk IKE/IPsec-beleid is geconfigureerd op VPN-gateways voor P2S?


**IKEv2**

|**Cipher** | **Gegevensintegriteit** | **PRF (PRF)** | **DH-groep** |
|---        | ---            | ---        | ---     |
|GCM_AES256 |    GCM_AES256    | SHA384    | GROUP_24 |
|GCM_AES256 |    GCM_AES256    | SHA384    | GROUP_14 |
|GCM_AES256 |    GCM_AES256    | SHA384    | GROUP_ECP384 |
|GCM_AES256 |    GCM_AES256    | SHA384    | GROUP_ECP256 |
|GCM_AES256 |    GCM_AES256    | SHA256    | GROUP_24 |
|GCM_AES256 |    GCM_AES256    | SHA256    | GROUP_14 |
|GCM_AES256 |    GCM_AES256    | SHA256    | GROUP_ECP384 |
|GCM_AES256 |    GCM_AES256    | SHA256    | GROUP_ECP256 |
|AES256     |   SHA384        | SHA384    | GROUP_24 |
|AES256     |   SHA384        | SHA384    | GROUP_14 |
|AES256     |   SHA384        | SHA384    | GROUP_ECP384 |
|AES256     |   SHA384        | SHA384    | GROUP_ECP256 |
|AES256     |   SHA256        | SHA256    | GROUP_24 |
|AES256     |   SHA256        | SHA256    | GROUP_14 |
|AES256     |   SHA256        | SHA256    | GROUP_ECP384 |
|AES256     |   SHA256        | SHA256    | GROUP_ECP256 |
|AES256     |   SHA256        | SHA256    | GROUP_2 |

**Ipsec**

|**Cipher** | **Gegevensintegriteit** | **PFS-groep** |
|---        | ---            | ---        |
|GCM_AES256    | GCM_AES256 | GROUP_NONE |
|GCM_AES256    | GCM_AES256 | GROUP_24 |
|GCM_AES256    | GCM_AES256 | GROUP_14 |
|GCM_AES256    | GCM_AES256 | GROUP_ECP384 |
|GCM_AES256    | GCM_AES256 | GROUP_ECP256 |
| AES256    | SHA256 | GROUP_NONE |
| AES256    | SHA256 | GROUP_24 |
| AES256    | SHA256 | GROUP_14 |
| AES256    | SHA256 | GROUP_ECP384 |
| AES256    | SHA256 | GROUP_ECP256 |
| AES256    | SHA1 | GROUP_NONE |

### <a name="what-tls-policies-are-configured-on-vpn-gateways-for-p2s"></a><a name="TLS policies"></a>Welk TLS-beleid is geconfigureerd op VPN-gateways voor P2S?
**TLS**

|**Beleid** |
|---| 
|TLS_ECDHE_ECDSA_WITH_AES_128_GCM_SHA256 |
|TLS_ECDHE_ECDSA_WITH_AES_256_GCM_SHA384 |
|TLS_ECDHE_RSA_WITH_AES_128_GCM_SHA256 |
|TLS_ECDHE_RSA_WITH_AES_256_GCM_SHA384 |
|TLS_ECDHE_ECDSA_WITH_AES_128_CBC_SHA256 |
|TLS_ECDHE_ECDSA_WITH_AES_256_CBC_SHA384 |
|TLS_ECDHE_RSA_WITH_AES_128_CBC_SHA256 |
|TLS_ECDHE_RSA_WITH_AES_256_CBC_SHA384 |
|TLS_RSA_WITH_AES_128_GCM_SHA256 |
|TLS_RSA_WITH_AES_256_GCM_SHA384 |
|TLS_RSA_WITH_AES_128_CBC_SHA256 |
|TLS_RSA_WITH_AES_256_CBC_SHA256 |

### <a name="how-do-i-configure-a-p2s-connection"></a><a name="configure"></a>Hoe configureer ik een P2S-verbinding?

Een P2S-configuratie vereist een flink aantal specifieke stappen. De volgende artikelen bevatten de stappen om u door de P2S-configuratie te laten lopen en koppelingen om de VPN-clientapparaten te configureren:

* [Een P2S-verbinding configureren - RADIUS-verificatie](point-to-site-how-to-radius-ps.md)

* [Een P2S-verbinding configureren - Azure native certificaatverificatie](vpn-gateway-howto-point-to-site-rm-ps.md)

* [OpenVPN configureren](vpn-gateway-howto-openvpn.md)

## <a name="next-steps"></a>Volgende stappen

* [Een P2S-verbinding configureren - RADIUS-verificatie](point-to-site-how-to-radius-ps.md)

* [Een P2S-verbinding configureren - Azure native certificaatverificatie](vpn-gateway-howto-point-to-site-rm-ps.md)

**"OpenVPN" is een handelsmerk van OpenVPN Inc.**
