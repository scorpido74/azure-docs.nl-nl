---
title: Informatie over Azure Point-to-Site VPN-verbindingen | VPN-gateway
description: Dit artikel helpt u de point-to-site-verbindingen te begrijpen en helpt u te beslissen welk P2S VPN-gatewayverificatietype u wilt gebruiken.
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: conceptual
ms.date: 02/19/2020
ms.author: cherylmc
ms.openlocfilehash: 78ed07560fdb15efb2de13c194549f5b433b775a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "77500607"
---
# <a name="about-point-to-site-vpn"></a>Over Point-to-Site VPN

Met een point-to-site-VPN-gatewayverbinding (P2S) kunt u vanaf een afzonderlijke clientcomputer een beveiligde verbinding maken met uw virtuele netwerk. Een P2S-verbinding wordt tot stand gebracht door deze te starten vanaf de clientcomputer. Deze oplossing is handig voor telewerkers die verbinding willen maken met een Azure VNet vanaf een externe locatie, zoals vanaf thuis of een conferentie. P2S-VPN is ook een uitstekende oplossing in plaats van een S2S-VPN wanneer u maar een paar clients hebt die verbinding moeten maken met een VNet. Dit artikel is van toepassing op het Resource Manager-implementatiemodel.

## <a name="what-protocol-does-p2s-use"></a><a name="protocol"></a>Welk protocol gebruikt P2S?

Point-to-site VPN kan een van de volgende protocollen gebruiken:

* **OpenVPN® Protocol**, een SSL/TLS gebaseerd VPN-protocol. Een SSL VPN-oplossing kan firewalls binnendringen, omdat de meeste firewalls TCP-poort 443 uitgaand openen, die SSL gebruikt. OpenVPN kan worden gebruikt om verbinding te maken vanaf Android, iOS (versies 11.0 en hoger), Windows, Linux en Mac-apparaten (OSX-versies 10.13 en hoger).

* Secure Socket Tunneling Protocol (SSTP), een eigen SSL-gebaseerd VPN-protocol. Een SSL VPN-oplossing kan firewalls binnendringen, omdat de meeste firewalls TCP-poort 443 uitgaand openen, die SSL gebruikt. SSTP wordt alleen ondersteund op Windows-apparaten. Azure ondersteunt alle versies van Windows met SSTP (Windows 7 en hoger).

* IKEv2 VPN, een op standaarden gebaseerde IPsec VPN-oplossing. IKEv2 VPN kan worden gebruikt om verbinding te maken vanaf Mac-apparaten (OSX-versie 10.11 en hoger).


>[!NOTE]
>IKEv2 en OpenVPN voor P2S zijn alleen beschikbaar voor het Resource Manager-implementatiemodel. Ze zijn niet beschikbaar voor het klassieke implementatiemodel.
>

## <a name="how-are-p2s-vpn-clients-authenticated"></a><a name="authentication"></a>Hoe worden P2S VPN-clients geverifieerd?

Voordat Azure een P2S VPN-verbinding accepteert, moet de gebruiker eerst worden geverifieerd. Er zijn twee mechanismen die Azure biedt om een verbindende gebruiker te verifiëren.

### <a name="authenticate-using-native-azure-certificate-authentication"></a>Verifiëren met native Azure-certificaatverificatie

Bij het gebruik van de native Azure-certificaatverificatie wordt een clientcertificaat dat op het apparaat aanwezig is, gebruikt om de verbindende gebruiker te verifiëren. Clientcertificaten worden gegenereerd op basis van een vertrouwd rootcertificaat en vervolgens op elke clientcomputer geïnstalleerd. U een rootcertificaat gebruiken dat is gegenereerd met een Enterprise-oplossing of u een zelfondertekend certificaat genereren.

De validatie van het clientcertificaat wordt uitgevoerd door de VPN-gateway en gebeurt tijdens het opzetten van de P2S VPN-verbinding. Het basiscertificaat is vereist voor de validatie en moet worden geüpload naar Azure.

### <a name="authenticate-using-native-azure-active-directory-authentication"></a>Verifiëren met native Azure Active Directory-verificatie

Azure AD-verificatie stelt gebruikers in staat om verbinding te maken met Azure met behulp van hun Azure Active Directory-referenties. Native Azure AD-verificatie wordt alleen ondersteund voor OpenVPN-protocol en Windows 10 en vereist het gebruik van de [Azure VPN-client.](https://go.microsoft.com/fwlink/?linkid=2117554)

Met native Azure AD-verificatie u gebruikmaken van de voorwaardelijke toegang van Azure AD en de MFA-functies (Multi-Factor Authentication) voor VPN.

Op hoog niveau moet u de volgende stappen uitvoeren om Azure AD-verificatie te configureren:

1. [Een Azure Active Directory-tenant configureren](openvpn-azure-ad-tenant.md)

2. [Azure AD-verificatie inschakelen op de gateway](openvpn-azure-ad-tenant.md#enable-authentication)

3. [Azure VPN-client downloaden en configureren](https://go.microsoft.com/fwlink/?linkid=2117554)


### <a name="authenticate-using-active-directory-ad-domain-server"></a>Verifiëren met Active Directory (AD) Domain Server

Met verificatie van AD-domein kunnen gebruikers verbinding maken met Azure met behulp van hun domeinreferenties voor de organisatie. Hiervoor is een RADIUS-server nodig die integreert met de AD-server. Organisaties kunnen ook gebruikmaken van hun bestaande RADIUS-implementatie.
  
De RADIUS-server kan on-premises of in uw Azure VNet worden geïmplementeerd. Tijdens de verificatie fungeert de Azure VPN Gateway als een doortocht en doorstuurt verificatieberichten heen en weer tussen de RADIUS-server en het verbindingsapparaat. Gateway bereikbaarheid naar de RADIUS-server is dus belangrijk. Als de RADIUS-server on-premises aanwezig is, is een VPN S2S-verbinding van Azure naar de on-premises site vereist voor bereikbaarheid.  
  
De RADIUS-server kan ook worden geïntegreerd met AD-certificaatservices. Hiermee u de RADIUS-server en de implementatie van uw bedrijfscertificaat voor P2S-certificaatverificatie gebruiken als alternatief voor de Azure-certificaatverificatie. Het voordeel is dat u geen rootcertificaten hoeft te uploaden en certificaten hoeft in te trekken naar Azure.

Een RADIUS-server kan ook worden geïntegreerd met andere externe identiteitssystemen. Dit opent tal van authenticatieopties voor P2S VPN, inclusief multi-factor opties.

![point-to-site](./media/point-to-site-about/p2s.png "Punt-naar-site")

## <a name="what-are-the-client-configuration-requirements"></a>Wat zijn de vereisten voor clientconfiguratie?

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

## <a name="which-gateway-skus-support-p2s-vpn"></a><a name="gwsku"></a>Welke gateway SKU's ondersteunen P2S VPN?

[!INCLUDE [aggregate throughput sku](../../includes/vpn-gateway-table-gwtype-aggtput-include.md)]

* Zie [Instellingen voor VPN-gateway voor](vpn-gateway-about-vpn-gateway-settings.md#gwsku)gateway-SKU-aanbevelingen.

>[!NOTE]
>De basis-SKU biedt geen ondersteuning voor IKEv2- of RADIUS-verificatie.
>

## <a name="what-ikeipsec-policies-are-configured-on-vpn-gateways-for-p2s"></a><a name="IKE/IPsec policies"></a>Welk IKE/IPsec-beleid is geconfigureerd op VPN-gateways voor P2S?


**IKEv2**

|**Cipher** | **Gegevensintegriteit** | **PRF (PRF)** | **DH-groep** |
|---        | ---           | ---       | ---   |
|GCM_AES256 |   GCM_AES256  | SHA384    | GROUP_24 |
|GCM_AES256 |   GCM_AES256  | SHA384    | GROUP_14 |
|GCM_AES256 |   GCM_AES256  | SHA384    | GROUP_ECP384 |
|GCM_AES256 |   GCM_AES256  | SHA384    | GROUP_ECP256 |
|GCM_AES256 |   GCM_AES256  | SHA256    | GROUP_24 |
|GCM_AES256 |   GCM_AES256  | SHA256    | GROUP_14 |
|GCM_AES256 |   GCM_AES256  | SHA256    | GROUP_ECP384 |
|GCM_AES256 |   GCM_AES256  | SHA256    | GROUP_ECP256 |
|AES256     |   SHA384      | SHA384    | GROUP_24 |
|AES256     |   SHA384      | SHA384    | GROUP_14 |
|AES256     |   SHA384      | SHA384    | GROUP_ECP384 |
|AES256     |   SHA384      | SHA384    | GROUP_ECP256 |
|AES256     |   SHA256      | SHA256    | GROUP_24 |
|AES256     |   SHA256      | SHA256    | GROUP_14 |
|AES256     |   SHA256      | SHA256    | GROUP_ECP384 |
|AES256     |   SHA256      | SHA256    | GROUP_ECP256 |
|AES256     |   SHA256      | SHA256    | GROUP_2 |

**Ipsec**

|**Cipher** | **Gegevensintegriteit** | **PFS-groep** |
|---        | ---           | ---       |
|GCM_AES256 | GCM_AES256 | GROUP_NONE |
|GCM_AES256 | GCM_AES256 | GROUP_24 |
|GCM_AES256 | GCM_AES256 | GROUP_14 |
|GCM_AES256 | GCM_AES256 | GROUP_ECP384 |
|GCM_AES256 | GCM_AES256 | GROUP_ECP256 |
| AES256    | SHA256 | GROUP_NONE |
| AES256    | SHA256 | GROUP_24 |
| AES256    | SHA256 | GROUP_14 |
| AES256    | SHA256 | GROUP_ECP384 |
| AES256    | SHA256 | GROUP_ECP256 |
| AES256    | SHA1 | GROUP_NONE |

## <a name="what-tls-policies-are-configured-on-vpn-gateways-for-p2s"></a><a name="TLS policies"></a>Welk TLS-beleid is geconfigureerd op VPN-gateways voor P2S?
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

## <a name="how-do-i-configure-a-p2s-connection"></a><a name="configure"></a>Hoe configureer ik een P2S-verbinding?

Een P2S-configuratie vereist een flink aantal specifieke stappen. De volgende artikelen bevatten de stappen om u door de P2S-configuratie te laten lopen en koppelingen om de VPN-clientapparaten te configureren:

* [Een P2S-verbinding configureren - RADIUS-verificatie](point-to-site-how-to-radius-ps.md)

* [Een P2S-verbinding configureren - Azure native certificaatverificatie](vpn-gateway-howto-point-to-site-rm-ps.md)

* [OpenVPN configureren](vpn-gateway-howto-openvpn.md)

### <a name="to-remove-the-configuration-of-a-p2s-connection"></a>De configuratie van een P2S-verbinding verwijderen

Zie de [veelgestelde vragen](#removeconfig)voor stappen hieronder.
 
## <a name="faq-for-native-azure-certificate-authentication"></a><a name="faqcert"></a>Veelgestelde vragen voor native Azure-certificaatverificatie

[!INCLUDE [vpn-gateway-point-to-site-faq-include](../../includes/vpn-gateway-faq-p2s-azurecert-include.md)]

## <a name="faq-for-radius-authentication"></a><a name="faqradius"></a>Veelgestelde vragen over RADIUS-verificatie

[!INCLUDE [vpn-gateway-point-to-site-faq-include](../../includes/vpn-gateway-faq-p2s-radius-include.md)]

## <a name="next-steps"></a>Volgende stappen

* [Een P2S-verbinding configureren - RADIUS-verificatie](point-to-site-how-to-radius-ps.md)

* [Een P2S-verbinding configureren - Azure native certificaatverificatie](vpn-gateway-howto-point-to-site-rm-ps.md)

**"OpenVPN" is een handelsmerk van OpenVPN Inc.**
