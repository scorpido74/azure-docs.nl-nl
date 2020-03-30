---
title: Voorbeeldconfiguratie voor het verbinden van Cisco ASA-apparaten met Azure VPN-gateways
description: In dit artikel vindt u een voorbeeldconfiguratie voor het verbinden van Cisco ASA-apparaten met Azure VPN-gateways.
services: vpn-gateway
author: yushwang
ms.service: vpn-gateway
ms.topic: article
ms.date: 10/19/2018
ms.author: yushwang
ms.openlocfilehash: 96e5c26ea7b5f1baa33fd8830491ee3aa1e60221
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75778079"
---
# <a name="sample-configuration-cisco-asa-device-ikev2no-bgp"></a>Voorbeeldconfiguratie: Cisco ASA-apparaat (IKEv2/no BGP)
In dit artikel vindt u voorbeeldconfiguraties voor het verbinden van ASA-apparaten (Cisco Adaptive Security Appliance) op Azure VPN-gateways. Het voorbeeld is van toepassing op Cisco ASA-apparaten waarop IKEv2 wordt uitgevoerd zonder het Border Gateway Protocol (BGP). 

## <a name="device-at-a-glance"></a>Apparaat in één oogopslag

|                        |                                   |
| ---                    | ---                               |
| Apparaatleverancier          | Cisco                             |
| Apparaatmodel           | ASA                               |
| Doelversie         | 8.4 en hoger                     |
| Getest model           | ASA 5505                          |
| Geteste versie         | 9.2                               |
| IKE-versie            | IKEv2                             |
| BGP                    | Nee                                |
| Azure VPN-gatewaytype | Vpn-gateway op basis van route           |
|                        |                                   |

> [!NOTE]
> De voorbeeldconfiguratie verbindt een Cisco ASA-apparaat met een **Azure-routegebaseerde VPN-gateway.** De verbinding maakt gebruik van een aangepast IPsec/IKE-beleid met de optie **UsePolicyBasedTrafficSelectors,** zoals beschreven in [dit artikel.](vpn-gateway-connect-multiple-policybased-rm-ps.md)
>
> Het voorbeeld vereist dat ASA-apparaten het **IKEv2-beleid** gebruiken met op toegangslijst gebaseerde configuraties, niet vti-gebaseerd. Raadpleeg de specificaties van de leveranciers van uw VPN-apparaat om te controleren of het IKEv2-beleid wordt ondersteund op uw on-premises VPN-apparaten.


## <a name="vpn-device-requirements"></a>Vereisten voor VPN-apparaten
Azure VPN-gateways gebruiken de standaard IPsec/IKE-protocolsuites om Vpn-tunnels (Site-to-Site) op te zetten. Zie [Over VPN-apparaten](vpn-gateway-about-vpn-devices.md)voor de gedetailleerde IPsec/IKE-protocolparameters en standaardcryptografische algoritmen voor Azure VPN-gateways.

> [!NOTE]
> U optioneel een exacte combinatie van cryptografische algoritmen en belangrijke sterke punten opgeven voor een specifieke verbinding, zoals beschreven in [Over cryptografische vereisten.](vpn-gateway-about-compliance-crypto.md) Als u een exacte combinatie van algoritmen en sterke punten opgeeft, moet u de bijbehorende specificaties op uw VPN-apparaten gebruiken.

## <a name="single-vpn-tunnel"></a>Eén VPN-tunnel
Deze configuratie bestaat uit één S2S VPN-tunnel tussen een Azure VPN-gateway en een on-premises VPN-apparaat. U de BGP optioneel configureren in de VPN-tunnel.

![Enkele S2S VPN-tunnel](./media/vpn-gateway-3rdparty-device-config-cisco-asa/singletunnel.png)

Zie [Single VPN-tunnelsetup](vpn-gateway-3rdparty-device-config-overview.md#singletunnel)voor stapsgewijze instructies voor het bouwen van de Azure-configuraties.

### <a name="virtual-network-and-vpn-gateway-information"></a>Informatie over virtuele netwerk- en VPN-gateway
In deze sectie worden de parameters voor het voorbeeld weergegeven.

| **Parameter**                | **Waarde**                    |
| ---                          | ---                          |
| Voorvoegsels voor virtueel netwerkadres        | 10.11.0.0/16<br>10.12.0.0/16 |
| IP-gateway azure VPN-gateway         | Azure_Gateway_Public_IP      |
| On-premises adresvoorvoegsels | 10.51.0.0/16<br>10.52.0.0/16 |
| On-premises IP-apparaat VPN-apparaat    | OnPrem_Device_Public_IP     |
| * Virtueel netwerk BGP ASN                | 65010                        |
| * Azure BGP-peer-IP           | 10.12.255.30                 |
| * On-premises BGP ASN         | 65050                        |
| * On-premises BGP peer IP     | 10.52.255.254                |
|                              |                              |

\*Optionele parameter alleen voor BGP.

### <a name="ipsecike-policy-and-parameters"></a>IPsec/IKE-beleid en -parameters
In de volgende tabel worden de IPsec/IKE-algoritmen en parameters weergegeven die in het voorbeeld worden gebruikt. Raadpleeg de specificaties van uw VPN-apparaat om de algoritmen te verifiëren die worden ondersteund voor uw VPN-apparaatmodellen en firmwareversies.

| **IPsec/IKEv2**  | **Waarde**                            |
| ---              | ---                                  |
| IKEv2-versleuteling | AES256                               |
| IKEv2-integriteit  | SHA384                               |
| DH-groep         | DHGroup24                            |
| * IPsec-versleuteling | AES256                               |
| * IPsec Integriteit  | SHA1                                 |
| PFS-groep        | PFS24                                |
| QM SA-levensduur   | 7.200 seconden                         |
| Verkeersselector | GebruikPolicyBasedTrafficSelectors $True |
| Vooraf gedeelde sleutel   | PreSharedKey                         |
|                  |                                      |

\*Op sommige apparaten moet IPsec-integriteit een null-waarde zijn wanneer het IPsec-versleutelingsalgoritme AES-GCM is.

### <a name="asa-device-support"></a>ASA-apparaatondersteuning

* Ondersteuning voor IKEv2 vereist ASA versie 8.4 en hoger.

* Ondersteuning voor DH Group en PFS Group buiten Groep 5 vereist ASA versie 9.x.

* Ondersteuning voor IPsec-versleuteling met AES-GCM en IPsec-integriteit met SHA-256, SHA-384 of SHA-512 vereist ASA-versie 9.x. Deze ondersteuningsvereiste is van toepassing op nieuwere ASA-apparaten. Op het moment van publicatie ondersteunen ASA-modellen 5505, 5510, 5520, 5540, 5550 en 5580 deze algoritmen niet. Raadpleeg de specificaties van uw VPN-apparaat om de algoritmen te verifiëren die worden ondersteund voor uw VPN-apparaatmodellen en firmwareversies.


### <a name="sample-device-configuration"></a>Voorbeeldapparaatconfiguratie
Het script biedt een voorbeeld dat is gebaseerd op de configuratie en parameters die in de vorige secties worden beschreven. De S2S VPN-tunnelconfiguratie bestaat uit de volgende onderdelen:

1. Interfaces en routes
2. Toegangslijsten
3. IKE-beleid en -parameters (fase 1 of hoofdmodus)
4. IPsec-beleid en -parameters (fase 2 of snelle modus)
5. Andere parameters, zoals TCP MSS-klemmen

> [!IMPORTANT]
> Voer de volgende stappen uit voordat u het voorbeeldscript gebruikt. Vervang de tijdelijke aanduidingswaarden in het script door de apparaatinstellingen voor uw configuratie.

* Geef de interfaceconfiguratie op voor zowel binnen- als buiteninterfaces.
* Identificeer de routes voor uw binnen/privé en buiten/openbare netwerken.
* Zorg ervoor dat alle namen en beleidsnummers uniek zijn op uw apparaat.
* Zorg ervoor dat de cryptografische algoritmen worden ondersteund op uw apparaat.
* Vervang de volgende **tijdelijke aanduidingswaarden** door werkelijke waarden voor uw configuratie:
  - Naam buiteninterface: **buiten**
  - **Azure_Gateway_Public_IP**
  - **OnPrem_Device_Public_IP**
  - IKE: **Pre_Shared_Key**
  - Namen van virtuele netwerk- en lokale netwerkgateways: **VNetName** en **LNGName**
  - Voorvoegsels voor virtueel netwerk en on-premises **netwerkadressen**
  - Juiste **netmaskers**

#### <a name="sample-script"></a>Voorbeeldscript

```
! Sample ASA configuration for connecting to Azure VPN gateway
!
! Tested hardware: ASA 5505
! Tested version:  ASA version 9.2(4)
!
! Replace the following place holders with your actual values:
!   - Interface names - default are "outside" and "inside"
!   - <Azure_Gateway_Public_IP>
!   - <OnPrem_Device_Public_IP>
!   - <Pre_Shared_Key>
!   - <VNetName>*
!   - <LNGName>* ==> LocalNetworkGateway - the Azure resource that represents the
!     on-premises network, specifies network prefixes, device public IP, BGP info, etc.
!   - <PrivateIPAddress> ==> Replace it with a private IP address if applicable
!   - <Netmask> ==> Replace it with appropriate netmasks
!   - <Nexthop> ==> Replace it with the actual nexthop IP address
!
! (*) Must be unique names in the device configuration
!
! ==> Interface & route configurations
!
!     > <OnPrem_Device_Public_IP> address on the outside interface or vlan
!     > <PrivateIPAddress> on the inside interface or vlan; e.g., 10.51.0.1/24
!     > Route to connect to <Azure_Gateway_Public_IP> address
!
!     > Example:
!
!       interface Ethernet0/0
!        switchport access vlan 2
!       exit
!
!       interface vlan 1
!        nameif inside
!        security-level 100
!        ip address <PrivateIPAddress> <Netmask>
!       exit
!
!       interface vlan 2
!        nameif outside
!        security-level 0
!        ip address <OnPrem_Device_Public_IP> <Netmask>
!       exit
!
!       route outside 0.0.0.0 0.0.0.0 <NextHop IP> 1
!
! ==> Access lists
!
!     > Most firewall devices deny all traffic by default. Create access lists to
!       (1) Allow S2S VPN tunnels between the ASA and the Azure gateway public IP address
!       (2) Construct traffic selectors as part of IPsec policy or proposal
!
access-list outside_access_in extended permit ip host <Azure_Gateway_Public_IP> host <OnPrem_Device_Public_IP>
!
!     > Object group that consists of all VNet prefixes (e.g., 10.11.0.0/16 &
!       10.12.0.0/16)
!
object-group network Azure-<VNetName>
 description Azure virtual network <VNetName> prefixes
 network-object 10.11.0.0 255.255.0.0
 network-object 10.12.0.0 255.255.0.0
exit
!
!     > Object group that corresponding to the <LNGName> prefixes.
!       E.g., 10.51.0.0/16 and 10.52.0.0/16. Note that LNG = "local network gateway".
!       In Azure network resource, a local network gateway defines the on-premises
!       network properties (address prefixes, VPN device IP, BGP ASN, etc.)
!
object-group network <LNGName>
 description On-Premises network <LNGName> prefixes
 network-object 10.51.0.0 255.255.0.0
 network-object 10.52.0.0 255.255.0.0
exit
!
!     > Specify the access-list between the Azure VNet and your on-premises network.
!       This access list defines the IPsec SA traffic selectors.
!
access-list Azure-<VNetName>-acl extended permit ip object-group <LNGName> object-group Azure-<VNetName>
!
!     > No NAT required between the on-premises network and Azure VNet
!
nat (inside,outside) source static <LNGName> <LNGName> destination static Azure-<VNetName> Azure-<VNetName>
!
! ==> IKEv2 configuration
!
!     > General IKEv2 configuration - enable IKEv2 for VPN
!
group-policy DfltGrpPolicy attributes
 vpn-tunnel-protocol ikev1 ikev2
exit
!
crypto isakmp identity address
crypto ikev2 enable outside
!
!     > Define IKEv2 Phase 1/Main Mode policy
!       - Make sure the policy number is not used
!       - integrity and prf must be the same
!       - DH group 14 and above require ASA version 9.x.
!
crypto ikev2 policy 1
 encryption       aes-256
 integrity        sha384
 prf              sha384
 group            24
 lifetime seconds 86400
exit
!
!     > Set connection type and pre-shared key
!
tunnel-group <Azure_Gateway_Public_IP> type ipsec-l2l
tunnel-group <Azure_Gateway_Public_IP> ipsec-attributes
 ikev2 remote-authentication pre-shared-key <Pre_Shared_Key> 
 ikev2 local-authentication  pre-shared-key <Pre_Shared_Key> 
exit
!
! ==> IPsec configuration
!
!     > IKEv2 Phase 2/Quick Mode proposal
!       - AES-GCM and SHA-2 requires ASA version 9.x on newer ASA models. ASA
!         5505, 5510, 5520, 5540, 5550, 5580 are not supported.
!       - ESP integrity must be null if AES-GCM is configured as ESP encryption
!
crypto ipsec ikev2 ipsec-proposal AES-256
 protocol esp encryption aes-256
 protocol esp integrity  sha-1
exit
!
!     > Set access list & traffic selectors, PFS, IPsec proposal, SA lifetime
!       - This sample uses "Azure-<VNetName>-map" as the crypto map name
!       - ASA supports only one crypto map per interface, if you already have
!         an existing crypto map assigned to your outside interface, you must use
!         the same crypto map name, but with a different sequence number for
!         this policy
!       - "match address" policy uses the access-list "Azure-<VNetName>-acl" defined 
!         previously
!       - "ipsec-proposal" uses the proposal "AES-256" defined previously 
!       - PFS groups 14 and beyond requires ASA version 9.x.
!
crypto map Azure-<VNetName>-map 1 match address Azure-<VNetName>-acl
crypto map Azure-<VNetName>-map 1 set pfs group24
crypto map Azure-<VNetName>-map 1 set peer <Azure_Gateway_Public_IP>
crypto map Azure-<VNetName>-map 1 set ikev2 ipsec-proposal AES-256
crypto map Azure-<VNetName>-map 1 set security-association lifetime seconds 7200
crypto map Azure-<VNetName>-map interface outside
!
! ==> Set TCP MSS to 1350
!
sysopt connection tcpmss 1350
!
```

## <a name="simple-debugging-commands"></a>Eenvoudige foutopsporingsopdrachten

Gebruik de volgende ASA-opdrachten voor foutopsporingsdoeleinden:

* De IPsec- of IKE-beveiligingskoppeling (SA) weergeven:
    ```
    show crypto ipsec sa
    show crypto ikev2 sa
    ```

* Voer de foutopsporingsmodus in:
    ```
    debug crypto ikev2 platform <level>
    debug crypto ikev2 protocol <level>
    ```
    De `debug` opdrachten kunnen aanzienlijke uitvoer genereren op de console.

* De huidige configuraties op het apparaat weergeven:
    ```
    show run
    ```
    Gebruik `show` subopdrachten om specifieke onderdelen van de apparaatconfiguratie weer te geven, bijvoorbeeld:
    ```
    show run crypto
    show run access-list
    show run tunnel-group
    ```

## <a name="next-steps"></a>Volgende stappen
Zie [Active-active VPN-gateways configureren](vpn-gateway-activeactive-rm-powershell.md)voor actieve cross-premises en VNet-naar-VNet-verbindingen.
